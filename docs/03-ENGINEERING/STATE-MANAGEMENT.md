# State Management (current)

Status: VERIFIED. Commit `2dd2fe4`.

## Approach
**flutter_bloc.** Two blocs provided app-wide in `main.dart`:
- `WorkBloc` — dashboard/trips/labours/attendance + navigation-for-next-trip orchestration.
- `HistoryBloc` — history & analytics data.

Sealed state + event classes (`work_state.dart`, `work_event.dart`, `history_*.dart`). Emitters drive UI via `BlocConsumer`/`BlocBuilder`/`BlocListener`.

## WorkBloc state set
`WorkInitial, WorkLoading, DashboardLoaded{currentWork,currentTrips,totalLabourCount,morningTripCount,eveningTripCount,totalTrips,searchQuery}, TripDetailsLoaded{work,tripLabours,labours}, WorkEmpty{message}, WorkError{message}, WorkActionSuccess, NavigateToConfirmNextTripState{...}`.

## WorkBloc events
LoadDashboardData, AddQuickTrip (empty handler — dead), NavigateToConfirmNextTrip, SaveNextTrip, RemoveLatestTrip, DeleteSpecificTrip, LoadTripDetails, SaveFullWorkTrip, SaveTripLabour, SaveLabour, UpdateTripLabour, DeleteTripLabour, SearchDashboard, FilterDashboard.

## Important behaviours / smell
- `_onSearchDashboard` sets `_currentSearchQuery` (a private field), then re-dispatches `LoadDashboardDataEvent`; filtering is applied inside the dashboard-load handler using the private field. **Search/filter state lives in BLoC private mutable fields, not in the emitted state** (`DashboardLoaded.searchQuery` is echoed but is excluded from `props`, so it doesn't drive rebuild equality).
- `FilterDashboardEvent` handler stores `_currentFilters` but the load path never actually applies filter predicates (`matchesFilter=true` always) → filtering effectively unimplemented (PARTIAL).
- Navigation-to-next-trip is emitted as a **state** (`NavigateToConfirmNextTripState`) that the dashboard listener reacts to by pushing a route — navigation-as-state coupling.
- After emitting `NavigateToConfirmNextTripState`, `_onNavigateToConfirmNextTrip` re-emits the captured `DashboardLoaded` — multi-emit per event.
- `WorkActionSuccess` is a transient state consumed by listeners to pop/snackbar.

## HistoryBloc
`LoadHistoryEvent` → `HistoryLoading → HistoryLoaded{groupedTrips:Date→Session→Trips, worksMap}/HistoryEmpty/HistoryError`. Aggregation (grouping) is done in the bloc.

## Loading/empty/error surfaced
- Screens switch exhaustively on sealed states. Several screens render transient states as `SizedBox.shrink` (routing states) — acceptable but note.

## Findings
| ID | Finding | Severity |
|---|---|---|
| S-1 | Mutable private filter/search state not reflected in state equality | MEDIUM |
| S-2 | Filter predicates not implemented | MEDIUM |
| S-3 | Empty `AddQuickTripEvent` handler | LOW |
| S-4 | Navigation triggered from state listener | MEDIUM (architecture) |
| S-5 | Multi-emit per navigation event | LOW |

## Native
Recommended UDF/MVI: single `UiState` in `StateFlow` with explicit Loading/Success/Empty/Error/Offline/Unauthorized/Forbidden; one-way events; avoid navigation-as-state (see `08-NATIVE-ANDROID/ANDROID-ARCHITECTURE.md`).
