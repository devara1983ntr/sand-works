# Testing (current) — overview

Status: VERIFIED presence & scope; execution UNVERIFIED (no Flutter SDK in environment). Commit `2dd2fe4`.

## Current test suite (VERIFIED files)
| Area | Files | Coverage intent |
|---|---|---|
| Unit — blocs | `work_bloc_test`, `history_bloc_test`, `labour_persistence_test`, `search_filter_test` | state transitions, search/filter, labour persistence |
| Unit — usecases | `trip_numbering_test`, `date_partition_test`, `get_works_usecase_test` | business rules |
| Unit — core | `failures_test`, `date_time_utils_test` | utilities |
| Unit — settings | `restore_logic_test` | backup parsing/validation rules |
| Widget | `dashboard_screen_test`, `dashboard_regression_test`, `dashboard_state_test`, `trip_edit_test`, `trip_details_screen_test`, `history_screen_test`, `analytics_screen_test`, `empty_state_widget_test` | per-screen behaviour |
| Integration | `continuity_test`, `edit_flow_test`, `final_manual_qa_simulation_test`, `benchmark_trip_labour_insert` | data integrity & flows |
| Performance | `trip_labour_loading_benchmark_test`, `trip_labour_write_benchmark_test` | load/write benchmarks |
| E2E/QA gate | `e2e/final_qa_gate_test.dart` | holistic gate |
| Helpers | `helpers/mock_work_repository.dart` | mock repo (test-only) |

Total: 27 test files, ~2,505 lines.

## Important honesty note (VERIFIED, no fake testing)
- Mocks are confined to tests (`mock_work_repository.dart`); production uses real Hive. No fake/dummy production backend, no hardcoded accounts. Good.
- **BUT** `restore_logic_test.dart` states it tests validation rules indirectly (writes files & re-checks rules) because the parse logic is a private top-level function — not a true unit test of the function. Coverage gap.

## Execution status
This environment has **no Flutter/Dart SDK**, so `flutter test` could not be re-run; pass/fail state and any counts are `UNVERIFIED` here. Prior agent docs claim a passing gate; not independently reproduced.

## Native test strategy (PROPOSED)
Unit (ViewModel/usecase/repository), Firebase integration (emulator suite), Compose UI tests (create/edit/delete/attendance/search/history), navigation tests, accessibility tests, offline/error/RBAC/security tests, performance (macrobench), release tests. Critical user journeys must have automated coverage. See `08-NATIVE-ANDROID/`.
