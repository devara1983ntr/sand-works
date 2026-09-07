# Performance Opportunities (current + native)

Status: current VERIFIED; native PROPOSED. Commit `2dd2fe4`.

## Quick wins in the current Flutter app (PROPOSED; audit-only phase — do not implement now)
1. Bundle `google_fonts` fonts (remove runtime fetch).
2. Index labour lookups (id→name map) instead of per-row `firstWhere` scans.
3. Move backup serialisation off-thread/streamed; it already uses `compute` for restore parse.
4. Replace the fixed 1.5 s splash with a readiness-gated start.
5. Reduce unconditional shimmer/scale loops; honour reduced motion.
6. Periodically compact Hive.

## Native Compose opportunities (PROPOSED)
- State: stable `@Stable`/immutable UI state; minimise recomposition; keys for `LazyColumn`.
- Data: use indexed queries/Room or Firestore queries with composite index + pagination; never load whole history for a KPI.
- Images: Coil with sized + memory cache (future profile/media).
- Concurrency: `Dispatchers.IO` for DB/network; main-safe ViewModels; Flow conflated where latency allowed.
- Startup: baseline profiles + startup profiling; defer Firebase init; avoid work on main thread.
- Release: R8/minify with rules; resource shrinking.
- Measurement: macrobenchmark (startup, scrolling), microbenchmark for repository; LeakCanary & memory profiling; track cold start and scroll jank in CI.
- Network (when added): retry/backoff, timeouts, cache, offline queue metrics.

## Evidence constraints
Do not claim improvements without measurement. Add macrobenchmarks before/after. All numeric targets above are aspirations, not measured current values (`UNVERIFIED`).
