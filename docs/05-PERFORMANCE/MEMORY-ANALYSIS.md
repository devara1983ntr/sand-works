# Memory Analysis (current)

Status: VERIFIED static; runtime measurement UNVERIFIED. Commit `2dd2fe4`.

## Static observations
- In-memory state: `DashboardLoaded` holds full trip lists for a session; `TripDetailsLoaded` holds the full labour master list plus trip labours. At single-user scale this is small.
- Analytics builds a **flat list** of all historical trips in memory (`analytics_screen.dart`) and History holds the full grouped map (`HistoryLoaded`) — grows with total history. Acceptable today; note for large datasets.
- Backup/restore: JSON-encodes/decodes all records and reads a file (≤25 MB) via an isolate (`compute`) — bounded by the 25 MB cap, which the code chose explicitly to avoid isolate overflow.
- No known leaks flagged statically; the app holds a few global singletons (get_it, Hive boxes) intentionally.
- `glassmorphism_ui` blur effects can be GPU/memory-expensive when many cards are on screen (dashboard uses several) — worth profiling.

## Native (PROPOSED)
- LazyColumn/LazyGrid with stable keys; paging for history/reports; Coil memory cache sizing; Room/DB queries not whole-list-in-memory; avoid holding full labour list when only names are needed (indexed lookup). Macrobenchmark & memory profiling (LeakCanary) in CI/QA.

## Verification status
VERIFIED static concerns. Actual RSS/heap numbers UNVERIFIED.
