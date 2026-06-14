# App Store 운영 템플릿

이 문서는 App Store Connect 등록, 심사 대응, 릴리즈 노트 작성 때 재사용하는 운영 템플릿입니다.
기능 사실관계는 `docs/requirements.md`, 제품 방향은 `docs/planning.md`를 기준으로 유지합니다.
버전별 스크린샷과 제출 참고 자료는 `docs/app_store/` 아래에 보관합니다.

## 1. 현재 기준

- 앱 이름: `RunHeat`
- 핵심 기능: Apple 건강 앱의 러닝 운동 데이터를 연간 히트맵으로 시각화
- HealthKit 권한: `HKObjectType.workoutType()` 읽기 전용
- 사용 데이터: 러닝 운동 데이터만 사용
- 공유 기능: 연간/월간/주간 히트맵 이미지 공유

## 2. 등록 문구

### 짧은 설명

- 한국어: `RunHeat는 Apple 건강 앱의 러닝 운동 데이터를 기반으로 히트맵을 생성하는 러닝 시각화 앱입니다.`
- 영어: `RunHeat is a running visualization app that generates heatmaps based on running workout data from the Apple Health app.`

### 확장 설명

- 한국어: `RunHeat는 Apple 건강 앱에 저장된 러닝 운동 데이터를 연도별 히트맵으로 보여주는 러닝 기록 시각화 앱입니다. 날짜별 거리 흐름을 한눈에 확인하고, 특정 날짜의 세션별 거리와 페이스를 빠르게 살펴볼 수 있습니다.`
- 영어: `RunHeat is a running visualization app that turns your running workout data from the Apple Health app into a yearly heatmap. You can quickly review your distance patterns by day and check session-level distance and pace for specific dates.`

## 3. 프로모션 텍스트 후보

App Store 프로모션 텍스트는 짧고 핵심 가치가 바로 드러나는 문구 위주로 관리합니다.

### 한국어 후보

- `Apple 건강 앱의 러닝 기록을 연간 히트맵으로 한눈에 확인하세요.`
- `러닝 거리 흐름을 히트맵으로 보고, 날짜별 세션 기록을 빠르게 확인하세요.`
- `내 러닝 기록을 연도·월·주 단위 히트맵으로 보고 공유하세요.`

### 영어 후보

- `See your running history from Apple Health at a glance with a yearly heatmap.`
- `Track your running patterns with a heatmap and inspect session details by date.`
- `View and share your running history in yearly, monthly, and weekly heatmaps.`

## 4. 앱 설명 템플릿

App Store 상세 설명용 초안입니다. 실제 등록 시 길이에 맞춰 축약하거나 확장합니다.

### 한국어

`RunHeat는 Apple 건강 앱에 저장된 러닝 운동 데이터를 히트맵으로 시각화해 주는 앱입니다. 연간 러닝 기록을 한 화면에서 살펴보며, 언제 얼마나 달렸는지 빠르게 파악할 수 있습니다.

날짜별 총 러닝 거리는 색상 강도로 표현되고, 특정 날짜를 선택하면 세션별 시작 시각, 거리, 페이스를 바로 확인할 수 있습니다. 또한 연간, 월간, 주간 기록을 이미지 카드로 미리보고 공유할 수 있어 자신의 러닝 흐름을 간단하게 정리하고 전달하기 좋습니다.

RunHeat는 러닝 운동 데이터만 사용하며, Apple 건강 앱의 운동 기록 읽기 권한만 요청합니다.`

### 영어

`RunHeat visualizes your running workout data from the Apple Health app as a heatmap, making it easy to review your running history at a glance. With a yearly view, you can quickly understand when you ran and how your distance patterns changed over time.

Daily running distance is represented with color intensity, and you can tap a specific date to see session-level details such as start time, distance, and pace. RunHeat also lets you preview and share yearly, monthly, and weekly heatmap cards, making it easy to summarize and share your running activity.

RunHeat only uses running workout data and requests read-only access to workout records from Apple Health.`

## 5. 핵심 기능 요약

### 한국어

- Apple 건강 앱의 러닝 운동 데이터 기반 연간 히트맵
- 날짜별 거리 강도 시각화
- 날짜 선택 시 세션별 거리와 페이스 확인
- 연간/월간/주간 히트맵 이미지 공유
- 러닝 운동 데이터만 사용

### 영어

- Yearly heatmap based on running workout data from Apple Health
- Daily distance intensity visualization
- Session-level distance and pace for selected dates
- Shareable yearly, monthly, and weekly heatmap cards
- Focused only on running workout data

## 6. 심사용 메모 템플릿

심사 메모는 기능 설명과 권한 사용 목적을 간결하게 적습니다.

### 한국어

- `이 앱은 Apple 건강 앱의 러닝 운동 데이터를 읽어 연간 히트맵 형태로 시각화합니다.`
- `HealthKit에서는 운동 데이터 읽기 권한만 요청하며, 러닝 운동만 조회합니다.`
- `사용자는 날짜별 세션 거리와 페이스를 확인하고, 연/월/주 단위 히트맵 이미지를 공유할 수 있습니다.`

### 영어

- `This app reads running workout data from Apple Health and visualizes it as a yearly heatmap.`
- `It requests read-only HealthKit access to workout data and only uses running workouts.`
- `Users can review session-level distance and pace by date and share yearly, monthly, and weekly heatmap images.`

## 7. 권한 설명

### HealthKit

- 사용 데이터
  - 러닝 운동 데이터만 사용
- 권한 범위
  - `HKObjectType.workoutType()` 읽기 전용
- 사용자 가치
  - 러닝 기록을 히트맵과 날짜별 세션 정보로 시각화

## 8. 스크린샷 카피 후보

App Store 제출용 실제 스크린샷은 빌드된 iPhone에서 캡처한 뒤 후처리하는 방식을 기준으로 합니다.

### 한국어

- `올해의 러닝을 한눈에`
- `매일의 거리와 흐름을 히트맵으로 빠르게 확인`
- `궁금한 날만 바로 열어보기`
- `화면 이동 없이 세션별 거리와 페이스 확인`
- `내 러닝 기록을 깔끔하게 공유`
- `연간·월간·주간 기록을 카드 한 장으로`

## 9. 제출 전 확인 항목

아래 항목은 이 저장소 기준으로 아직 별도 확정본이 없습니다.

- 앱 부제
- 키워드
- 지원 URL
- 마케팅 URL
- 개인정보처리방침 URL
- 연령 등급 대응 문구

## 10. 릴리즈 노트 템플릿

- 버전: `<version>`
- 기준 변경: `<주요 변경 요약>`

### 한국어

- `<사용자에게 보이는 주요 변경 1>`
- `<사용자에게 보이는 주요 변경 2>`
- `<품질 개선 또는 안정화 변경>`

### 영어

- `<User-facing change 1>`
- `<User-facing change 2>`
- `<Quality or reliability improvement>`

## 11. 릴리즈 노트 예시

### 1.3

한국어:

- `연간, 월간, 주간 러닝 히트맵을 이미지로 공유할 수 있는 기능을 추가했어요.`
- `히트맵의 장거리 배지와 월 시작일 표시를 다듬어 가독성을 개선했어요.`
- `연도별 새로고침과 로딩 동작을 정리해 더 부드럽게 탐색할 수 있어요.`

영어:

- `Added image sharing for yearly, monthly, and weekly running heatmaps.`
- `Improved heatmap readability with refined long-run badges and month-start indicators.`
- `Polished year refresh and loading behavior for a smoother browsing experience.`

## 12. 심사 제출 기록

- `2026-05-17`
  - `1.3` 버전을 App Store Connect 심사에 제출함
