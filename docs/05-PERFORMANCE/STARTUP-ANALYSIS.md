# Startup Analysis (current)

Status: VERIFIED (static flow); timing `UNVERIFIED`. Commit `2dd2fe4`.

## Startup sequence (VERIFIED)
1. OS launches `MainActivity` (`FlutterActivity`) with launch theme → Flutter engine boots.
2. `main()` runs: `WidgetsFlutterBinding.ensureInitialized()`, `HiveSetup.init()` (registers 5 adapters + opens 5 boxes on the app-support filesystem), `di.init()` (get_it), then `runApp`.
3. `/splash` shows for ~1.5 s (a fixed delay), then `/dashboard`.
4. Dashboard dispatches `LoadDashboardDataEvent`; shows skeleton while loading.

## Observations
- **Fixed 1.5 s splash** adds latency regardless of readiness (it is decorative, not a load-gate). Could gate on first-frame/data-ready instead.
- Hive opens 5 boxes synchronously awaited in `main()` before `runApp` — can add to cold start on slow storage; acceptable at single-user scale.
- No heavy image assets (only a 192px icon). Fonts may fetch over network (if used at startup) delaying text style resolution.

## Native (PROPOSED) targets
- Cold start ≤ 2 s on mid-range device (target, not current claim).
- Firebase initialised lazily; baseline profile; minimal startup work off main thread; skip decorative splash or make it a proper data-gate.

## Verification status
VERIFIED startup structure. Measured cold-start time UNVERIFIED (no device in environment).
