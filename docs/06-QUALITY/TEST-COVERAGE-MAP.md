# Test Coverage Map (current)

Status: VERIFIED file map; actual line coverage UNVERIFIED (not measured here). Commit `2dd2fe4`.

## Feature → test mapping
| Feature/rule | Covered by test(s) | Notes |
|---|---|---|
| Trip numbering (morning/evening/new date) | `unit/usecases/trip_numbering_test` | VERIFIED presence |
| Date partition/isolation | `unit/usecases/date_partition_test` | |
| GetWorks | `unit/usecases/get_works_usecase_test` | |
| WorkBloc transitions | `unit/blocs/work_bloc_test`, `widget/dashboard_state_test` | |
| Search/filter (bloc) | `unit/blocs/search_filter_test` | filter predicates not implemented; test likely search only |
| Labour persistence | `unit/blocs/labour_persistence_test` | |
| History bloc | `unit/blocs/history_bloc_test` | |
| DateTime utils / failures | `unit/core/*` | |
| Backup/restore validation | `unit/settings/restore_logic_test` | indirect (see T-2) |
| Dashboard UI | `widget/dashboard_screen_test`, `dashboard_regression_test`, `trip_edit_test` | |
| Trip details UI | `widget/details/trip_details_screen_test` | |
| History UI | `widget/history/history_screen_test` | |
| Analytics UI | `widget/analytics/analytics_screen_test` | |
| Shared empty-state | `widget/shared/empty_state_widget_test` | |
| Continuity & edit flow | `integration/continuity_test`, `edit_flow_test` | |
| Manual QA sim / gate | `integration/final_manual_qa_simulation_test`, `e2e/final_qa_gate_test` | |
| Performance | `performance/*`, `integration/benchmark_trip_labour_insert` | microbench |

## Not covered (VERIFIED absence)
- Settings Backup UI flow (widget) & the full restore transaction (rollback path) — only validation logic indirectly tested.
- Settings screen widget test missing.
- Details screen (orphaned) no dedicated widget test (dead-code branch implies not covered).
- AddEditWorkScreen draft autosave/restore UI test.
- ConfirmNextTripScreen save flow UI test.
- Error/retry UI states.
- Accessibility, localization, security tests — none.

## Native target
Compose UI + Espresso mapped 1:1 to journeys; see `08-NATIVE-ANDROID/`.
