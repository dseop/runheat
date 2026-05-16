# 달리기 1km 구간별 페이스(Splits) 구현 계획

## 1. 개요
선택된 러닝 세션(Workout)의 1km 구간별 페이스 정보를 표시하기 위해 HealthKit의 `.distanceWalkingRunning` 샘플 데이터를 수동으로 집계(Manual Aggregation)하는 방식을 도입합니다. 추가적인 별도 상세 화면을 만들지 않고 기존 히트맵 오버레이(카드) 내에서 확장하여 보여줍니다.

## 2. 주요 변경 파일 및 컨텍스트
- **모델 (`Models/RunSession.swift`)**: 구간별 기록을 저장할 모델 추가 및 기존 모델 식별자 업데이트.
- **매니저 (`Managers/HealthDataProviding.swift`, `HealthKitManager.swift`, `MockHealthKitManager.swift`)**: HealthKit에서 샘플 데이터를 조회하고 1km 단위로 계산하는 쿼리 로직 구현.
- **뷰 (`Views/RunHeatmapView.swift`)**: 오버레이 카드 내 러닝 세션을 탭했을 때 스플릿 정보를 펼쳐서 보여주는 UI 추가.

## 3. 구현 단계 (Implementation Steps)

### A. 데이터 모델 업데이트
1. **`RunSplit` 구조체 추가**: 
   - 1km 구간 번호, 누적 거리, 소요 시간, 페이스 정보를 담는 모델 작성.
2. **`RunSession` 식별자 변경**:
   - 기존 무작위 `UUID()` 대신 `HKWorkout.uuid`를 `id`로 사용하여 HealthKit 쿼리 시 원본 워크아웃을 참조할 수 있도록 수정.
   - 뷰 바인딩 및 캐싱을 위해 `splits: [RunSplit]?` 속성 추가.

### B. HealthKit 매니저 계층 구현
1. **`HealthDataProviding` 프로토콜 업데이트**:
   - `func fetchSplits(for workoutId: UUID) async throws -> [RunSplit]` 메서드 정의.
2. **`HealthKitManager` 구현**:
   - 전달받은 `workoutId`로 특정 워크아웃의 시간 범위를 설정.
   - `HKSampleQuery`를 사용하여 해당 시간 동안의 `.distanceWalkingRunning` 수치(`HKQuantitySample`) 목록을 조회.
   - 조회된 샘플 목록을 시간순으로 순회하며 거리(미터)와 시간을 누적.
   - 누적 거리가 1000m에 도달할 때마다 하나의 `RunSplit` 객체를 생성하고, 다음 1km 구간 계산을 위해 변수를 초기화.
   - (마지막 잔여 거리는 1km 미만이더라도 마지막 구간으로 기록).
3. **`MockHealthKitManager` 구현**:
   - 시뮬레이터 환경 테스트를 위해 무작위 1km 스플릿 데이터를 생성하여 반환하는 로직 추가.

### C. UI 연동 (오버레이 확장)
1. **`RunHeatmapView` 오버레이 뷰 컴포넌트 수정**:
   - 현재 오버레이에 표시되는 개별 세션 행(`HStack`)을 탭 가능한 버튼으로 변경.
   - 세션 탭 시 `fetchSplits`를 비동기로 호출하며, 로딩 중에는 인디케이터를 표시.
   - 로딩 완료 후, 해당 세션 바로 아래에 아코디언(Accordion) 스타일로 스플릿 리스트를 펼침.
   - 리스트 항목은 `1km: 5'30"`, `2km: 5'45"`와 같이 직관적으로 표시.
   - 공간 제약을 피하기 위해 최대 표시 항목 수를 제한하거나, 컴팩트한 레이아웃을 사용.

## 4. 검증 및 기대 효과
- 사용자는 화면 이동 없이 메인 히트맵에서 즉각적으로 특정 날짜의 세부 구간 페이스를 확인할 수 있습니다.
- 실제 HealthKit 데이터(샘플) 기반으로 계산하므로, Apple 워치 기본 운동 앱 외의 타사 앱(예: NRC, Strava 등)으로 기록된 러닝 데이터도 동일하게 1km 단위 스플릿을 지원할 수 있습니다.
