# FitDash 개발 문서

이 문서는 프로젝트의 디렉터리 책임, 핵심 타입, 데이터 흐름, 유지보수 포인트를 빠르게 파악하기 위한 개발 기준 문서입니다.

## 1. 기술 스택

- 언어: Swift
- UI 프레임워크: SwiftUI
- 데이터 계층: HealthKit
- 아키텍처 성격: 단순한 View + Manager + Model 분리

## 2. 저장소 구조

- `fitdash/fitdashApp.swift`
  - 앱 진입점
  - `ContentView`를 최초 화면으로 연결
- `fitdash/ContentView.swift`
  - 초기 권한 요청
  - 첫 데이터 로딩 트리거
  - `HealthKitManager`를 환경 객체로 주입
- `fitdash/Managers/`
  - HealthKit 접근 추상화와 실제 조회 구현
- `fitdash/Models/`
  - 도메인 모델과 날짜 단위 집계 모델
- `fitdash/Views/`
  - 히트맵 메인 화면과 보조 뷰
- `fitdash/Resources/`
  - 시뮬레이터 및 개발용 JSON 데이터
- `fitdashTests/`
  - 단위 테스트

## 3. 핵심 타입

### 3.1 앱 진입

- `fitdashApp`
  - 앱의 `@main`
  - 윈도우 그룹의 루트로 `ContentView`를 사용

### 3.2 초기 화면

- `ContentView`
  - 앱 시작 시 `HealthKitManager.shared`를 주입한다.
  - 인증은 한 번만 요청한다.
  - 초기 fetch 시작과 실패 종료를 분리해서 상태를 관리한다.

### 3.3 데이터 계층

- `HealthDataProviding`
  - HealthKit 접근 추상화 프로토콜
  - 실제 구현과 Mock 구현의 공통 인터페이스
- `HealthKitManager`
  - 실제 HealthKit 조회 담당
  - 러닝 워크아웃만 필터링
  - 초기 로딩 상태와 오류 상태를 관리
  - HealthKit 조회 결과와 RunHeat 로컬 보강 기록을 병합하여 기존 `RunSession` 인터페이스로 제공
- `MockHealthKitManager`
  - 시뮬레이터용 목 데이터 제공자
  - UI 미리보기와 개발 검증용
- `JSONRunRecordStore`
  - Apple Health에 없는 외부/import 러닝 기록을 보관하는 로컬 JSON 저장소
  - HealthKit 전체 데이터 복제 용도가 아니라 보강 기록 저장 용도
- `RunDataAggregator`
  - HealthKit 기록과 로컬 보강 기록을 병합
  - 출처 ID 또는 시작 시각/거리/시간 근접값으로 중복 기록을 제거
- `StravaBulkExportImporter`
  - Strava Bulk Export의 `activities.csv`를 읽어 Run 계열 활동만 `RunRecord(source: .strava)`로 변환
  - 중복 CSV 컬럼과 `Moving Time`/`Elapsed Time` fallback을 처리
  - 현재 단계에서는 GPX 경로 파일을 파싱하지 않음

### 3.4 도메인 모델

- `RunSession`
  - 러닝 세션 단위 데이터
  - 시작/종료 시각, 거리, 시간, 페이스 계산 포함
  - 화면과 공유 기능이 사용하는 표시용 모델
  - 상세 데이터 확장을 위해 이동 시간, 전체 시간, 고도 상승, 평균/최대 심박, 경로 좌표를 선택 필드로 보관
- `RunRoutePoint`
  - 경로 지도 렌더링을 위한 위도/경도, 선택 고도, 선택 시각 좌표 모델
- `RunRecord`
  - 여러 출처의 러닝 기록을 정규화한 내부 모델
  - `RunSource`, 외부 ID, 시작/종료 시각, 거리, 시간, 상세 지표, 생성/수정 시각 포함
- `RunSource`
  - `appleHealth`, `strava`, `garminFile`, `manualImport` 출처 구분
  - 사용자 표시 목적보다 중복 제거와 재가져오기 추적을 위한 메타데이터
- `RunDay`
  - 날짜 단위로 세션을 그룹화한 모델
- `RunSessionDayDetails`
  - 특정 날짜의 세션 필터링과 총 거리 계산용 보조 로직
- `HeatmapSnapshot`
  - `연/월/주` 기간의 공유용 총합/일별 거리 스냅샷
  - 공유 이미지 렌더링 입력 모델
- `HeatmapSnapshotFactory`
  - 기간별 스냅숏 생성과 공유 가능 범위 계산

### 3.5 화면 구성

- `RunHeatmapView`
  - 연도 탭
  - 히트맵 레이아웃
  - 날짜 상세 오버레이
  - 로딩 및 새로고침 상태 표현
  - 연/월/주 합계 탭 기반 스냅숏 공유 진입
  - 개발 빌드에서 Strava `activities.csv` import 진입 제공
- `RunHeatmapCell`
  - 개별 날짜 셀 렌더링
- `HeatmapDisplayData`
  - 범용 기간의 주 단위 히트맵 행 데이터 생성
- `HeatmapSnapshotCardView`
  - 공유용 정적 히트맵 카드 렌더링
- `HeatmapSnapshotPreviewSheet`
  - 공유 이미지 렌더링 결과 미리보기와 시스템 공유 시트 호출
- `ShareSheet`
  - `UIActivityViewController` 브리지
- `RunHeatmapFormatters`
  - 거리, 페이스, 날짜 문자열 포맷
- `RunHeatmapHelpers`
  - 레이아웃 보조 계산
- `RunListView`
  - 목록형 표현 보조 화면

## 4. 데이터 흐름

1. 앱 시작
2. `ContentView`에서 권한 요청
3. `HealthKitManager`가 러닝 워크아웃 조회
4. `RunSession` 배열 생성
5. `RunHeatmapView`가 날짜별/연도별 집계
6. `HeatmapDisplayData`가 주 단위 그리드 생성
7. 셀 렌더링과 오버레이 표시
8. 공유 요청 시 `HeatmapSnapshotFactory`가 기간 스냅숏 생성
9. `HeatmapSnapshotCardView`를 `ImageRenderer`로 이미지화
10. `HeatmapSnapshotPreviewSheet`와 시스템 공유 시트 표시

## 5. 개발 환경 규칙

- 시뮬레이터는 HealthKit 대신 Mock 데이터를 사용한다.
- 개발 전용 기능은 `#if DEBUG`로 제한한다.
- 인증 흐름은 `ContentView`에서 중복 실행되지 않게 유지한다.
- SwiftUI `Text`에 문자열 보간을 직접 넣을 때는 `Text(verbatim:)`을 우선 고려한다.

## 6. 유지보수 포인트

- 요구사항 변경 시 `docs/requirements.md`와 함께 정합성을 확인한다.
- 화면 구조 변경 시 이 문서와 `docs/requirements.md`의 정합성을 함께 확인한다.
- HealthKit 데이터 형태가 바뀌면 `Managers`와 `Models`를 먼저 확인한다.
- 히트맵 레이아웃 변경은 `Views/RunHeatmapView.swift`와 `HeatmapDisplayData.swift`를 함께 본다.

## 7. 테스트와 리소스

- 단위 테스트는 `fitdashTests/`에서 관리한다.
- 목 데이터는 `fitdash/Resources/`의 JSON 파일을 사용한다.
- 앱 아이콘과 색상은 `Assets.xcassets/`에서 관리한다.

## 8. TestFlight 배포 운영

- TestFlight 배포 작업은 `testflight-local-deploy` 브랜치에서 수행한다.
- 배포 시작 전 `testflight-local-deploy` 브랜치로 이동한 뒤 최신 `dev` 브랜치 내용을 반영한다.
- 배포 중 발견한 스크립트 문제, 코드서명 이슈 대응, 배포 문서 수정은 같은 브랜치에서 이어서 처리한다.
- 배포 작업이 끝나도 일반 기능 개발 브랜치와 배포 브랜치의 책임은 분리해서 유지한다.
