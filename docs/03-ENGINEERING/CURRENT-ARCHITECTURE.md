# Current Architecture — detailed

Status: VERIFIED. Commit `2dd2fe4`.

## Directory structure
```
lib/
├── main.dart                    # boot: Hive init -> DI init -> runApp
├── routes/app_router.dart       # go_router config (splash/shell/sub-routes)
├── theme/app_theme.dart         # M3 dark theme + tokens
├── config/di/injection_container.dart  # get_it wiring
├── core/
│   ├── database/hive_setup.dart # opens 5 boxes + registers adapters
│   ├── error/failures.dart      # Failure, DatabaseFailure, ValidationFailure
│   ├── usecases/usecase.dart    # UseCase<T,P> (dartz Either)
│   └── utils/date_time_utils.dart
├── features/
│   ├── dashboard/presentation/{splash,dashboard}_screen.dart
│   ├── history/presentation/history_screen.dart
│   ├── analytics/presentation/analytics_screen.dart
│   ├── settings/presentation/settings_screen.dart
│   ├── details/presentation/{details,trip_details}_screen.dart
│   └── work/
│       ├── data/{datasources,models,repositories}/
│       ├── domain/{entities,repositories,usecases}/
│       └── presentation/{bloc,screens}/
├── shared/
│   ├── main_layout.dart
│   └── widgets/ (empty_state, glass_card, custom_text_field, premium_button, skeleton_loading)
```

## Startup sequence
`main()` → `WidgetsFlutterBinding.ensureInitialized()` → `HiveSetup.init()` (registers 5 adapters, opens boxes) → `di.init()` → `runApp(MyApp)` → `ScreenUtilInit` → `MultiBlocProvider(WorkBloc, HistoryBloc)` → `MaterialApp.router`.

## Feature wiring
- `WorkBloc` is constructed with 15 usecases + a uuid; owns search query & filter private fields.
- `HistoryBloc` uses `GetWorksUseCase` + `GetTripsForWorkUseCase`.
- Repository impl maps `*Model → *Entity`; Data source owns Hive box keys & migration (`_migrateLegacyTripLabours`).
- Backups read Hive boxes directly in Settings (data-layer code embedded in a presentation screen).

## Concurrency & lifecycle
- Hive boxes opened once at startup and held as singletons.
- Box operations are async `put/get/delete`; no explicit transactions (restore emulates atomicity with snapshot+rollback).
- No background workers, no WorkManager (not needed offline single-user).

## Known architectural smells
| ID | Smell | Evidence |
|---|---|---|
| A-1 | Presentation accesses Hive directly | `dashboard_screen.dart`, `add_edit_work_screen.dart` |
| A-2 | Business aggregation in presentation | `analytics_screen.dart`, `history_screen.dart` grouping |
| A-3 | Duplicate `LabourFormModel` type across two screens | both create screens |
| A-4 | Duplicate `@override` annotations & duplicate switch cases | `work_local_data_source.dart`, `work_repository_impl.dart`, `trip_details_screen.dart` |
| A-5 | Backup/restore logic (data) inside a screen | `settings_screen.dart` top-level funcs |
| A-6 | Empty event handler `AddQuickTripEvent` | `work_bloc.dart` |

## Verification status
VERIFIED against code. Native re-architecture is separate (PROPOSED).
