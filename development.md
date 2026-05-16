# FitDash 개발 문서

이 문서는 코드 구조와 유지보수 포인트를 빠르게 파악하기 위한 문서입니다.
화면/동작의 상세 기준은 `docs/requirements.md`를 우선합니다.

## 1. 저장소 역할

- `fitdash/fitdashApp.swift`: 앱 진입점
- `fitdash/ContentView.swift`: 초기 권한 요청과 첫 데이터 로딩 진입점
- `fitdash/Managers/`: HealthKit 접근과 데이터 제공 계층
- `fitdash/Models/`: 도메인 모델과 일자 집계 모델
- `fitdash/Views/`: 히트맵 및 보조 화면
- `fitdash/Resources/`: 시뮬레이터 및 개발용 JSON 데이터
- `fitdashTests/`: 단위 테스트

## 2. 핵심 타입

- `HealthDataProviding`: 실제 구현과 Mock 구현이 공유하는 인터페이스
- `HealthKitManager`: 러닝 워크아웃 조회 책임
- `MockHealthKitManager`: 시뮬레이터/개발용 목 데이터 제공
- `RunSession`: 러닝 세션 단위 모델
- `RunDay`: 날짜 단위 집계 모델
- `RunSessionDayDetails`: 날짜 상세 조회 보조 로직
- `RunHeatmapView`: 히트맵 화면과 오버레이 조합
- `RunHeatmapCell`: 개별 날짜 셀 렌더링
- `YearHeatmapData`: 연간 히트맵 주 단위 데이터 생성

## 3. 읽는 순서

- 요구사항 변경: `docs/requirements.md`
- 제품 방향 확인: `docs/planning.md`
- HealthKit 데이터 참고: `docs/healthkit_data.md`
- 스플릿 기능 검토: `docs/plan_fetch_splits.md`
- 코드 수정 시: 관련 `Managers/` -> `Models/` -> `Views/` 순서로 확인

## 4. 유지보수 포인트

- 화면 구조를 바꿀 때는 `Views/RunHeatmapView.swift`와 `YearHeatmapData.swift`를 함께 본다.
- HealthKit 데이터 형태가 바뀌면 `Managers/`와 `Models/`를 먼저 확인한다.
- 목 데이터와 시뮬레이터 경로는 `MockHealthKitManager`와 `Resources/` 기준을 유지한다.
- 개발 전용 기능은 `#if DEBUG` 범위로 제한한다.

## 5. 리소스

- 단위 테스트는 `fitdashTests/`에서 관리한다.
- 앱 아이콘과 색상은 `Assets.xcassets/`에서 관리한다.

## 6. TestFlight 버전 정책

- 앱 버전(`MARKETING_VERSION`)은 사람이 직접 올린다. 기능 릴리스는 마이너 버전, 큰 변경은 메이저 버전을 올린다.
- 빌드 번호(`CURRENT_PROJECT_VERSION`)는 `scripts/beta.sh` 실행 중에만 임시 갱신한다.
- `BUILD_NUMBER` 환경변수가 있으면 그 값을 사용하고, 없으면 UTC 타임스탬프(`yyyyMMddHHmm`)를 사용한다.
- 같은 앱 버전 안에서는 TestFlight 빌드 번호만 계속 증가시켜 검증 빌드를 올린다.
- 스크립트는 아카이브/업로드 후 프로젝트의 빌드 번호를 원래 값으로 되돌린다. TestFlight용 타임스탬프 빌드 번호 변경은 커밋하지 않는다.
