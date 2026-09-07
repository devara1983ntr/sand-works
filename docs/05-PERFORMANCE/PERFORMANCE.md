# Performance (current) — overview

Status: analysis VERIFIED where possible; runtime numbers `UNVERIFIED` (no Flutter/device in sandbox; benchmarks present in repo but not re-run here). Commit `2dd2fe4`.

## Current posture
- Offline, single-user, Hive-backed. Realistic data volumes are small-to-moderate, but long history growth and many labours per trip are the main scaling concerns.
- The code shows deliberate performance intent for `TripLabour`: composite keys `"<tripId>_<id>"`, prefix-scan reads, and `putAll` bulk writes (`work_local_data_source.dart`).
- The repository contains benchmark/perf tests under `test/performance/` and `test/integration/benchmark_trip_labour_insert.dart`, indicating earlier measurement work by the authoring agent.

## Benchmark claims in repo (VERIFIED existence, numeric results NOT re-run here)
- `test/performance/trip_labour_loading_benchmark_test.dart`, `trip_labour_write_benchmark_test.dart`, `test/features/work/data/datasources/performance_benchmark_test.dart`, `test/integration/benchmark_trip_labour_insert.dart`.
- Because the Flutter SDK is unavailable in this environment, these were **not re-executed**; their pass/numbers are `UNVERIFIED`.

## Known risk areas (VERIFIED statically)
| Area | Concern |
|---|---|
| Full-box scans | `getWorks`, `getAllTrips`, `getLabours`, `getLaboursForTrips` iterate `box.values` |
| Name resolution | `trip_details_screen.dart` uses `firstWhere` over the full labour list per labour row → O(trips×labours) worst case |
| Backup serialize | Reads entire DB and JSON-encodes all rows (memory spike, esp. near 25 MB) |
| Fonts | `google_fonts` runtime network fetch can block/delay glyph rendering offline |
| Nested scrolling | Dashboard `ListView` with nested non-scroll lists; `ListView.builder` inside it |
| Database growth | No retention/compact strategy for Hive; delete leaves tombstones |

## Native (PROPOSED) performance priorities
Stable Compose state; `LazyColumn` keys; Coil image caching; paging for large lists; coroutine dispatchers (IO for DB/network); baseline profiles; R8; startup & macrobenchmarks; memory profiling. See `PERFORMANCE-OPPORTUNITIES.md`.
