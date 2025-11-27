# FitDash Mini Requirements

- **Platform:** iOS app built with Swift + SwiftUI.
- **HealthKit Access:** Request permission for `HKWorkoutActivityType.running` and read running workouts.
- **Run Session List:** Show each run with date, distance (km), and average pace (sec/km).
- **Calendar View:** Monthly calendar highlighting days with running distance totals.
- **Core Models:** `RunSession` (date, distanceMeters, durationSeconds, pace calculation) and `RunDay` (day-level aggregation).
- **Managers:** `HealthKitManager` handles authorization and workout fetching.
- **Target User:** Solo user (the owner) reviewing personal Apple Health running data.
