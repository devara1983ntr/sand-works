# FINAL PERFORMANCE CONTRACT — V1 (Phase 0.75)

Realistic, measurable targets where measurable; explicit measurement method where not yet possible. No invented magic numbers beyond feasibility. Reference audit PERFORMANCE + PERF gaps reconciled.

## Targets (measure during implementation; verify on device)
| Area | Target (feasible) | Measure | Notes |
|---|---|---|---|
| Cold start to interactive (Dashboard) | ≤ ~1.5–2 s on a mid device | Macrobenchmark (Startup) | skeleton first paint ≤ ~1 s; no blocking IO on main |
| Warm/navigation transitions | ≤ 60 fps sustained; jank-free lists | Macrobenchmark / FrameTiming | avoid main-thread Firestore work |
| Scroll | 60fps on ≥500-row history/report table | FrameTiming | paginate; no per-row DB |
| Firestore query response (online) | < ~300 ms p95 for owner-scoped indexed queries | manual/network traces | indexes per FINAL-QUERY-RULE-MATRIX |
| Local (Room) read (today's trips) | < ~100 ms | micro-bench | cache-first |
| Image/avatar load | < ~300 ms local, lazy network w/ cache | perf | Coil, no huge decode |
| Memory | no OOM; cache bounded (LRU); large rosters handled | Profiler | paginate attendance/reports |
| Battery | no wake-locks; WorkManager batch sync; no tight polling | battery profile | sync on connectivity/backoff |
| Sync/outbox | deterministic; large offline queue drains w/o ANR (WorkManager) | integration + log | dependency-ordered replay |
| Backup/export | progress + cancel; no ANR on big data (off-main, streamed) | test | ≤25 MB local file handled |
| Trip numbering compute | trivial; server-side; no client recompute cost | unit | |
| Report aggregates | NOT computed via full-history client scan | — | CF counters/derived (D-7) |

## Measurement method (where not pre-set)
- Macrobenchmark (Startup/FrameTiming) committed under `app/src/androidTest` baseline profiles; trace on a reference device (list model + config in performance doc). 
- Network/latency traces via Android Studio Profiler + Firebase Performance (optional). 
- Battery via profile; CPU/memory via Profiler on the data-size scenario.
- Targets are re-confirmed on device during implementation; they are goals, not pre-measured claims (no fabricated numbers).

## Anti-regressions
- No network/DB on main thread; paging for long lists; indexes for every query; no unbounded full-box scans (reference PERF-1/2); baseline profile on release path.

## Ops
- Crash-free sessions ≥99.5% (measure post-release via Crashlytics); alert on error rate & quota (FINAL-FIREBASE-SECURITY-MODEL monitoring).
