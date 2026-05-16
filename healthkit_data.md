# HealthKit 러닝 데이터 가이드

이 문서는 Apple HealthKit의 `HKWorkoutActivityType.running`을 통해 조회하거나 계산할 수 있는 주요 데이터 필드를 정리합니다.

## 1. 기본 워크아웃 정보 (HKWorkout)
워크아웃 세션이 완료되면 `HKWorkout` 객체에서 즉시 확인할 수 있는 요약 데이터입니다.

| 필드명 | 데이터 타입 | 설명 |
| :--- | :--- | :--- |
| **시작/종료 시간** | `Date` | 러닝이 시작되고 종료된 정확한 시점. |
| **총 이동 거리** | `HKQuantity` | 세션 전체의 누적 거리 (보통 미터/킬로미터 단위). |
| **총 소요 시간** | `TimeInterval` | 실제 운동 진행 시간 (일시정지 제외 가능). |
| **활성 에너지 소모량** | `HKQuantity` | 러닝 중 소모한 칼로리 (Active Calories). |
| **평균 페이스** | 계산값 | `총 거리 / 총 시간`으로 산출되는 전체 평균 속도. |

## 2. 표준 신체/운동 지표 (HKQuantityType)
워크아웃 기간 내에 샘플링되어 기록되는 세부 데이터입니다.

| 지표명 | HealthKit 식별자 (ID) | 설명 |
| :--- | : :--- | :--- |
| **심박수** | `.heartRate` | 운동 중 실시간 심박수 (BPM). |
| **걸음 수** | `.stepCount` | 러닝 중 기록된 총 걸음 수. |
| **오른 층계 수** | `.flightsClimbed` | 고도 변화가 있는 러닝 시 기록되는 층수 정보. |
| **최대 산소 섭취량** | `.vo2Max` | (실외 러닝 시) 추정된 심폐 지구력 지표. |

## 3. 고급 러닝 수치 (Running Form Metrics)
watchOS 9 및 iOS 16 이상에서 지원되는 전문적인 러닝 분석 데이터입니다.

| 지표명 | HealthKit 식별자 (ID) | 설명 |
| :--- | :--- | :--- |
| **러닝 파워** | `.runningPower` | 와트(Watt) 단위로 측정되는 운동 강도. |
| **러닝 속도** | `.runningSpeed` | 구간별/실시간 이동 속도 (m/s). |
| **보폭** | `.runningStrideLength` | 한 걸음당 이동 거리 (cm/m). |
| **수직 진동** | `.runningVerticalOscillation` | 달릴 때 몸이 위아래로 움직이는 정도 (cm). |
| **지면 접촉 시간** | `.runningGroundContactTime` | 발이 지면에 닿아 있는 시간 (ms). |

## 4. 경로 및 구간 정보 (Metadata & Events)
| 종류 | 설명 |
| :--- | :--- |
| **GPS 경로** | `HKWorkoutRoute`를 통해 위도/경도 좌표 리스트를 가져와 지도에 표시 가능. |
| **랩(Lap) 타임** | `HKWorkoutEvent`를 통해 사용자가 수동으로 찍은 랩 기록 조회. |
| **자동 구간(Segment)** | 1km 혹은 5km 단위로 시스템이 자동 분할한 기록. |

---

## 현재 앱 적용 현황 (`fitdash`)
- 현재 앱은 워크아웃 요약값만 사용한다.
- 적용 필드: 시작 시간, 종료 시간, 총 거리, 총 소요 시간.
- 경로, 심박수, 스플릿은 별도 기능 검토 대상이다.

---

## 5. HealthKit 데이터 파이프라인 가이드

`fitdash` 앱이 HealthKit에서 러닝 데이터를 조회하여 화면에 표시하기까지의 단계별 흐름입니다.

### 1단계: 권한 요청 및 확인
- **시점**: 앱 실행 시 (`ContentView.onAppear` 등) 또는 설정 화면.
- **역할**: 사용자의 건강 데이터 접근 허용 여부 확인.
- **코드**: `HKHealthStore().requestAuthorization(toShare: nil, read: [HKObjectType.workoutType()])`.

### 2단계: 쿼리 실행 (HKSampleQuery)
- **시점**: 메인 화면 진입 시 또는 새로고침 요청 시.
- **역할**: `HKWorkoutActivityType.running` 필터를 사용하여 워크아웃 샘플 조회.
- **코드**: `HKSampleQuery`를 사용하여 시작 날짜(`startDate`) 기준 내림차순 정렬로 `HKWorkout` 객체 리스트를 가져옴.

### 3단계: 도메인 모델 변환 (Mapping)
- **역할**: HealthKit의 원시 객체(`HKWorkout`)를 앱에서 사용하기 쉬운 Swift 모델(`RunSession`)로 변환.
- **변환 로직**:
  - `totalDistance` (m) → `distanceMeters` (Double)
  - `duration` (TimeInterval) → `durationSeconds` (Double)
  - `startDate`, `endDate` (Date) 그대로 유지.

### 4단계: 데이터 집계 및 저장 (Aggregation)
- **역할**: 개별 `RunSession`들을 일자별(`RunDay`) 또는 연도별(`YearHeatmapData`)로 묶어 히트맵 데이터를 생성.
- **코드**: `RunHeatmapView`에서 사용할 수 있도록 캘린더 계산기를 사용하여 날짜별 거리 합계를 산출.

### 5단계: UI 렌더링
- **역할**: 최종 가공된 데이터를 SwiftUI 뷰(`RunHeatmapCell`, `RunListView` 등)에 바인딩하여 시각화.
- **특이사항**: 시뮬레이터 환경에서는 `MockHealthKitManager`를 통해 더미 데이터를 생성하여 이 파이프라인을 대체함.
