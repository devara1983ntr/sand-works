# Module / Feature Dependency Map (current)

Status: VERIFIED. Commit `2dd2fe4`. (Feature directory names are logical, not separate build modules — single Flutter app module.)

## Dependency graph (logical)
```mermaid
flowchart TD
  APP[app: main] --> ROUTER[routes/app_router]
  APP --> THEME[theme]
  APP --> DI[config/di]
  DI --> WORK[features/work/data+domain]
  ROUTER --> DASH[dashboard] & HIST[history] & ANAL[analytics] & SET[settings] & DET[details] & WORK[work/presentation screens]
  DASH --> BLOC[work bloc]
  HIST --> HBLOC[history bloc]
  ANAL --> HBLOC
  DET --> BLOC
  WORK[work screens] --> BLOC
  BLOC --> UC[work usecases] --> REPOINT[work repo interface]
  REPOIMPL --> DS[work local data source]
  DS --> HIVE[hive]
  SHARED[shared/widgets + main_layout] --> all screens
```
- **app (main)** depends on everything via DI + router.
- **features/work** is the core domain/feature; others (dashboard/history/analytics/details/settings) are thin presentation consumers.
- **shared/** UI is consumed by all features.
- **No circular dependency** detected at file level (single package); pragmatic Hive touches by presentation break strict layering but not module cycles.

## Feature → data dependencies
| Feature dir | Uses domain/data | Notes |
|---|---|---|
| dashboard | WorkBloc | + direct Hive labour lookup (next-trip names) |
| details | WorkBloc | details + trip_details |
| history | HistoryBloc + WorkBloc | delete uses WorkBloc |
| analytics | HistoryBloc | aggregates |
| settings | Hive boxes directly | backup/restore embedded |
| work | its own data/domain | form draft → Hive direct |

## Findings
- Coupling of analytics & history to the same HistoryBloc is acceptable (shares load) but means analytics triggers a full history reload.
- Settings owns data-layer backup/restore logic (should be a service/repository in native).
- Duplicate `LabourFormModel` shared by two screens; would be a shared UI model in native.

## Native (PROPOSED)
Suggested Android modules if justified: `:app`, `:core`, `:feature:auth`, `:feature:work`/`:feature:jobs`, `:feature:crew`, `:feature:reports`, `:data:local`, `:data:remote`. Only split if the codebase & team size warrant (avoid over-modularization).
