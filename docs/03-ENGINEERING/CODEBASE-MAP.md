# Codebase Map

Status: VERIFIED (repo HEAD `2dd2fe4`, audit branch `audit/documentation`). Commit `2dd2fe4`.

## Repository snapshot
| Item | Value |
|---|---|
| Dart files under `lib/` | 47 (incl. 5 generated `.g.dart`) |
| Total lines under `lib/` | 6,140 |
| Test files under `test/` | 27 |
| Total test lines | ~2,505 |
| Dart LOC of top files | dashboard 543, settings 555, add_edit_work 635, work_bloc 469, trip_details 496 |

## lib/ map
- `main.dart` — boot.
- `routes/app_router.dart` — GoRouter.
- `theme/app_theme.dart`, `config/di/injection_container.dart`.
- `core/` — database (Hive), error, usecases, utils.
- `features/dashboard/`, `history/`, `analytics/`, `settings/`, `details/` — presentation screens.
- `features/work/` — full feature with data/domain/presentation.
- `shared/` — `main_layout.dart` + 5 widgets.

## Android scaffolding
- `android/app/build.gradle.kts` — namespace/applicationId `com.roshan.labourparty`, `minSdk 24`, compile/target via Flutter defaults, Java/Kotlin 17, release minify+shrink+proguard.
- `MainActivity.kt` — plain `FlutterActivity`.
- Launcher icons (mipmap), launch styles; no permissions in main manifest.
- No iOS/macOS/web/Windows scaffolding present.

## test/ map
- `test/unit/blocs/` (work_bloc, history_bloc, labour_persistence, search_filter), `test/unit/usecases/` (trip_numbering, date_partition, get_works), `test/unit/settings/restore_logic_test.dart`, `test/unit/core/` (failures, date_time_utils).
- `test/widget/` per-screen (dashboard x4, trip_details, history, analytics, shared empty-state).
- `test/integration/` (continuity, edit_flow, manual_qa_sim, benchmark insert), `test/performance/` (trip_labour load/write), `test/e2e/final_qa_gate_test.dart`.
- `test/helpers/mock_work_repository.dart`.

## Asset map
- `assets/branding/app_icon_192.png` (only asset).

## Docs (pre-existing) under `docs/`
Extensive agent-written docs exist at root of `docs/` (ARCHITECTURE, DATABASE, DESIGN_SYSTEM, NAVIGATION_FLOW, STATE_MANAGEMENT, TESTING, OFFLINE_STRATEGY, PERFORMANCE, PRODUCTION_OVERVIEW, TRIP_ENGINE, KNOWN_LIMITATIONS, BACKUP_RESTORE, BUSINESS_RULES, RELEASE, CONTRIBUTING, CHANGELOG, migration_notes, memory_bank, release/*, hotfix/*, validation/test reports, `production_readiness/*`, `screenshots/*`). This audit ADDS the numbered tree under `docs/` per the migration brief and does not overwrite those files except where intended (README & CHANGELOG referenced).

## Note on pre-existing docs
The repo already contains documentation claiming extensive release/production-readiness work. This audit re-verifies code truth independently and flags where pre-existing docs over-claim (e.g., current-execution claims). See `00-AUDIT-INDEX.md` and `BUG-DEFECT-REGISTER.md`.
