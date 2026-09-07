# OFFLINE & SYNC SPEC — SAND WORKS

App tolerates temporary network loss. No fake server success, no silent data loss, no duplicate trips/earnings, no overwriting newer server state. Idempotency keys on writes; conflicts explicit.

## Allowed offline behaviour
- Create local drafts.
- Queue permitted trip operations (driver/owner trip writes).
- Show clear offline state.
- Synchronise later (WorkManager deterministic replay).

## Forbidden
- Fake server success (a queued op must show "Queued/pending-sync", never "saved" until server ack).
- Silently losing queued data.
- Duplicate trips / duplicate earnings (idempotency).
- Overwriting newer server state (revision/conflict).

## Mechanics
- Room local cache + outbox (`opId`, type, payload, dependency group, state).
- Every retriable mutation carries a client `opId`; server/CF dedupes on opId (bounded window) → replay/retry never duplicates.
- Optimistic concurrency: editable docs carry `rev`; stale writes rejected → explicit conflict.
- Daily closure & earnings are server-authoritative & idempotent per (org,date); offline never fabricates a closure.
- Sync order respects dependencies (e.g., trip before its participants).
- Backoff + retry; session-expiry/offline queue preserved across re-auth (not dropped).

## Conflict handling
- Explicit, user-visible (keep mine / reload / where meaningful merge), never silent last-write-wins on owner/driver editable data.
- Server-derived values (tripNumber, money totals, closure, leaderboard) resolve server-side via CF re-derivation.

## UI indicators (per UX/quality)
Online · Offline banner · Stale (cached, "updated X ago") · Syncing (n) · Sync-failed [Retry] · Conflict · Recovered. Offline writes show pending/queue; never colour-only.

## Tests
Offline create/queue → reconnect → idempotent sync (no duplicate trip/earnings), ordering, conflict detection/resolution, retry dedupe, closure-not-double, no fake success, session-expiry preserving queue.
