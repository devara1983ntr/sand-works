# FINAL UI STATE CONTRACT — V1 (Phase 0.75)

Standardize skeleton/spinner/progress/empty/error/offline/stale/retry/sync/pending across ALL screens via a shared state-component library + a single `UiState` machine. No screen invents its own pattern. This is the LOADING + EMPTY + OFFLINE contract consolidated.

## Canonical visual primitives
| Indicator | Purpose | Usage | Notes |
|---|---|---|---|
| Skeleton | initial content load | full content region on first load | not a spinner over blank; shape-matches content |
| Button spinner | a submit/action in progress | primary CTA | disables double-tap |
| Refresh spinner (pull) | revalidation of shown content | list top | content stays visible (stale ok) |
| Footer spinner | pagination load-more | list bottom | |
| Progress bar | long op (export/backup/restore/upload) | content + cancel | off-main (WorkManager) |
| Pending/pulse dot | a local op queued offline | per row / sync chip | label "Queued/pending-sync" |
| Sync banner/chip | connectivity/outbox status | top | Syncing… (n), Pending, Offline, Conflict, Updated X ago |
| Stale tag | showing cached older data | top/section | "Updated X min ago" |
| Empty state | query returns zero expected data | content | icon + explanation + (optional) primary + secondary action; role-correct |
| Error state | operation failure | content | typed message + Retry |
| Forbidden | no permission (signed in) | content | message + allowed-area action |
| Unauthorized | not signed in | → N-03/N-08 | preserve draft/deep link |

## UiState machine (per screen)
INITIAL → LOADING(skeleton) → SUCCESS / EMPTY
SUCCESS --refetch--> REFRESHING (stale kept) --→ SUCCESS
Any action: SUBMITTING (button spinner) --→ SUCCESS | ERROR | PARTIAL_FAILURE | CONFLICT | (offline → queued pending)
Global overlays: OFFLINE (banner), SESSION_EXPIRED (intercept → N-08), FORBIDDEN/UNAUTHORIZED (content/modal).
Long ops: PROGRESS (with cancel) → SUCCESS | ERROR.

## Offline contract (no fake success)
- Connectivity monitor exposes Online/Offline.
- Reads: online → Firestore/live; offline → Room cache with STALE tag if older than threshold; no cache → OFFLINE-empty message (not "no data").
- Writes: online → direct (server ack) → SUCCESS. Offline → enqueue to outbox with opId; UI shows QUEUED/PENDING label on the item; banner "Syncing… (n pending)". On reconnect auto-sync idempotent → SUCCESS or CONFLICT/PARTIAL.
- A queued op must NEVER render as server-saved. Sync-failed → Sync-failed chip + Retry.
- Pending count is visible & actionable (retry/discard per item where allowed).

## Empty-state contract
Every empty list uses the shared EmptyState with an actionable next step (or intentionally none). EMPTY ≠ ERROR. Role-correct actions (owner sees "add", never a labourer-create CTA). Query-empty-due-to-filter → offer "clear filters".

## Retry contract
Single shared Retry control. Retries reuse opId (idempotent). Backoff for rate-limit.

## Accessibility of states
State changes announced via live region; indicators never colour-only (FINAL-ACCESSIBILITY).

## Test
FT-UI asserts each screen maps states to shared components; screenshots/layout tests per state; offline/empty/error per FINAL-TEST-CONTRACT.
