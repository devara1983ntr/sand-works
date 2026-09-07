# Error / Loading / Empty / Offline / Retry States

Purpose: document every screen/major operation's states. Status: VERIFIED current; native targets PROPOSED. Commit `2dd2fe4`.

## Cross-cutting guidance
Avoid bare "Something went wrong." without a recovery path. Below records current behaviour per area.

## 1. Loading
| Area | Current behaviour (VERIFIED) |
|---|---|
| Dashboard | Skeleton (GlassCard-shaped shimmer) |
| History/Analytics/Settings/Trip details/Details | Full-screen `CircularProgressIndicator` |
| Buttons | `PremiumButton` shows spinner when `isLoading` |
| Settings backup/restore | Full-screen spinner block (`_isLoading`) |

## 2. Empty
| Area | Current behaviour |
|---|---|
| Dashboard (no work today) | `WorkEmpty` → EmptyStateWidget "No work added today" + CTA |
| Recent-trips sublist empty | "No trips in current session" text |
| History | EmptyStateWidget "No historical work found" (blank CTA label — BUG-06) |
| Analytics | EmptyStateWidget "No data to analyze" (blank CTA label) |
| Trip details (no labours) | "No labours assigned to this trip" |

## 3. Error
| Area | Current behaviour | Recovery |
|---|---|---|
| Dashboard load error | Centred error-colour text of `Failure.message` | **No retry button** (gap) |
| Trip details load error | "Error loading details"/message text | No retry |
| History/Analytics | error message text | no dedicated retry (screen-level refresh icon on History) |
| Save/delete failures | Snackbar with `state.message` | implicit |
| Backup/restore | typed error dialogs (Too Large/Unsupported/Invalid/generic) | clear guidance |

## 4. Offline
Intrinsically offline (no network). No offline-specific states needed. When network/backend is added (native), define Offline state + queue/reconcile. Currently `NOT APPLICABLE` as a special case.

## 5. Partial failure
Not applicable — operations are single-source local (no multi-part network). Restore is all-or-nothing with rollback (VERIFIED). Full-trip save performs several writes; if a write throws mid-way the error is surfaced but **not** wrapped in a transaction (partial-write risk noted in performance/integrity). Watch in native.

## 6. Retry
- Dashboard/Trip error: none (gap). History: manual refresh icon. Recommend explicit Retry on all error states for native.

## 7. Cached fallback
Local Hive data is always the source; there is no remote cache. Not applicable.

## 8. Rate limiting / timeout / auth expiry
- No backend → rate limiting/timeouts/auth expiry do not apply today.
- Native: define when Firebase is added.

## Native design (PROPOSED)
Represent states explicitly in UI state: Loading, Success(Empty), Error, Offline, Unauthorized, Forbidden; each with recovery actions and semantics. Never a bare generic error.

## Verification status
VERIFIED current mapping. Runtime screenshot states under `docs/screenshots`.
