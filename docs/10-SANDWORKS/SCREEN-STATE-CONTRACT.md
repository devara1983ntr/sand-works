# SCREEN STATE / ERROR / LOADING / EMPTY / OFFLINE CONTRACT — SAND WORKS

Every screen uses ONE shared state model + component library. No screen invents its own pattern. Applies to Owner/Driver/Labourer screens.

## State vocabulary (sealed UiState per screen)
Idle · Validating · Loading · Submitting · Succeeded · Failed · Empty · Offline/Pending · Conflict · Unauthorized · Forbidden · SessionExpired · Cancelled · PartialFailure (batch writes).

## Canonical indicators
| Indicator | Purpose | Rule |
|---|---|---|
| Skeleton | initial content load | shapes match content; no spinner over blank |
| Button spinner | submit/action | disables double-tap |
| Refresh spinner (pull) | revalidation | content stays (stale ok) |
| Footer spinner | pagination | long lists |
| Progress bar | export/closure/photo/backup | + Cancel; off-main |
| Pending dot | offline-queued op | "Queued/pending-sync"; never "saved" until ack |
| Offline banner/chip | connectivity | Online/Offline/Syncing(n)/Sync-failed/Conflict/Recovered; not colour-only |
| Empty state | no data (truthful) | icon + explanation + role-correct action; EMPTY≠ERROR |
| Error state | operation failure | typed message + Retry (FINAL-ERROR pattern below) |
| Conflict | explicit | keep/reload/merge prompt |
| Forbidden | role blocked | message + allowed-area nav |
| Unauthorized | not signed in | → re-auth preserving draft/deep link |

## Error contract (types → message/recovery/retry/log/analytics/nav)
Validation · Authentication · Authorization/Forbidden · Not Found · Conflict · Network · Timeout · Offline(owned by offline) · Firebase/Firestore · Storage · Notification-send · Rate-limited · Server(CF) · Session-expired · Permission-denied(Android) · Unknown.
Rules: retry idempotent (opId); forbidden ≠ generic; offline ≠ error; no silent catch (AGENT §14.16); errors observable + logged (no PII/secrets).

## Loading honesty
Every loading state has a real initiating op + deterministic completion/failure. No forever-spinner, no artificial delay, no hiding missing impl behind a spinner (AGENT §14.17).

## Empty honesty
Real data / real backend / real user input / authoritative content / truthful empty. NEVER seeded fake lists/rankings. A labourer with no trips shows real zero/empty, not fabricated rows. Leaderboard with 1 worker shows 1 real rank only.

## Offline honesty (also OFFLINE-SYNC-SPEC)
Queued op clearly "pending-sync" until server ack. No fake server success, no loss, no duplicates, no overwriting newer state. Closure/money never fabricated offline.

## Per-role notable overrides
- Labourer screens: read-only; Submitting mostly n/a except notifications read.
- Money/leaderboard/export: content only from persisted real data; loading while deriving; empty where none.
- Alerts: prominent UI + ack; offline alert cannot fabricate delivery.

## Tests
Every screen mapped states → shared components + UI test (happy/empty/error/offline/forbidden/submitting/conflict); FT matrix in TEST-AND-QUALITY-SPEC.
