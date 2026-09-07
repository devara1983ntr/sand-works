# Performance Audit (current)

Status: VERIFIED static; numeric UNVERIFIED. Commit `2dd2fe4`. Severity for performance risks.

## Findings
| ID | Sev | Category | Finding | Evidence | Mitigation |
|---|---|---|---|---|---|
| PERF-1 | MED | DB | Full-value scans for whole collections (`getWorks`, `getAllTrips`, `getLabours`, `getLaboursForTrips`) | data source methods | Index by key date; batch; move to indexed store (native) |
| PERF-2 | MED | UI | Labour-name resolution scans full labour list per labour row (`firstWhere`) | trip_details | Build id→name map once; native: indexed join |
| PERF-3 | LOW | Data | Backup serialises entire DB in memory & writes large JSON | settings | Stream; cap; native background via WorkManager |
| PERF-4 | LOW | Font | `google_fonts` network fetch | app_theme | Bundle fonts |
| PERF-5 | LOW | UI | Loop/shimmer animations on FAB & counters (`repeat`) | dashboard | Reduce decoration; honour reduced motion |
| PERF-6 | INFO | DB | Hive compaction/tombstones on deletes unmanaged | — | periodic compact/repack |
| PERF-7 | INFO | Blocking | UI-triggered awaits on many sequential box writes (e.g., full-trip save loops) | bloc save handlers | Use batch `putAll`; native DB transactions |

## Verified good practice (VERIFIED)
- TripLabour composite-key prefix reads + `putAll` bulk writes (PERF intent present).
- Skeleton loading improves perceived startup.
- Benchmark/perf tests exist in the repo.

## Gaps in evidence
- No cold-start/session-time numbers measured in this environment. Prior repo perf reports exist under `docs/production_readiness/performance_report.md` but were **not** independently reproduced (`UNVERIFIED`).

## Verification status
VERIFIED structural. Numeric/perf thresholds UNVERIFIED without device+SDK.
