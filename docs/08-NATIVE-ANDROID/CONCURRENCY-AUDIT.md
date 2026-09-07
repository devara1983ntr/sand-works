# Concurrency Audit

Status: Phase 0.5 (PROPOSED). Defines duplicate/race/simultaneous/stale handling, idempotency, transactions, optimistic concurrency, server timestamps, conflict resolution.

## Problem classes
| Class | Scenario | Impact | Strategy |
|---|---|---|---|
| Double submission | double-tap/retry creates 2 trips | dupes | idempotency key (per user+op) on CF; disable button |
| Duplicate offline replay | reconnect flushes same op twice | dupes/overwrite | idempotency key persisted; dedupe on CF |
| Race (create unique session) | two devices create same date+session | dupes | transaction on CF uniqueness; conflict UI |
| Simultaneous edit | two admins edit same trip | lost update | `rev` optimistic concurrency; reject stale |
| Stale write | offline old doc overwrites newer online | data loss | version check before apply |
| Conflicting assignment | driver assigned twice / reassigned | ambiguous | assignment guarded; notify + state clear |
| Repeated notifications | CF retry sends duplicate | spam | server dedupe (event id) |
| Retry duplication (CF) | function retried after timeout | double side effect | idempotent function (eventId) |

## Concurrency mechanisms
- **Idempotency**: every mutating BO has a client-generated `opId`; CF stores processed `opId`s (per user) and returns prior result on replay. Covers offline retry, CF retry, double-tap.
- **Transactions**: cross-doc invariants (create session + counter; assign + notify; number allocation) run in CF Firestore transactions.
- **Optimistic concurrency**: editable docs carry `rev`; writes must match current rev or reject → Conflict UI. Applies to trip/session/attendance-correction/crew/profile.
- **Server timestamps**: `updatedAt`/`createdAt` from server (FieldValue.serverTimestamp); client never sets.
- **Conflict resolution policy** (owner-editable data): no silent last-write-wins where data loss possible. Present the two versions + "keep mine / keep theirs / merge" where feasible; attendance conflicts resolve to a prompt. Server-only-derived values (numbers, status) resolve via CF re-derivation. (Decision where fully automatic vs user-prompt — D-8/per-doc type.)
- **Repeated notifications**: server dedupe keyed on underlying event; NOTIFICATION-COVERAGE.
- **Outbox ordering**: offline ops flush in creation order; a later op depending on earlier waits until earlier applied (dependencies) to avoid stale application.

## Per-op concurrency rule (reference BO)
| BO | Idempotent | Transaction | Optimistic rev | Notes |
|---|---|---|---|---|
| BO-1 create session | yes | yes (uniqueness) | — | conflict if exists |
| BO-2 create trip | yes | yes (number) | — | — |
| BO-3 attendance update | yes (opId) | batch/multi | yes | per-row rev |
| BO-4 delete (soft) | yes | yes (cascade) | — | — |
| BO-5 close | yes | yes | — | state guard |
| BO-6/7 assign/status | yes | yes | rev on trip | notify dedupe |
| BO-8/9 crew/user | yes | yes (uniqueness) | rev | ≥1 owner |
| BO-13 restore | yes | multi-step (see DATA-CONSISTENCY) | — | one-shot id |

## Gaps flagged
- Optimistic `rev` on editable docs is an addition to reference (single-user; reference assumed no concurrency) → HIGH gap (multi-user product).
- Conflict-resolution UI (E9/E16/E25) needs explicit designs; recommended prompt-not-overwrite. 
- Idempotency-key store & dedupe must be server-side (client can't be trusted) → HIGH.
- Verification PROPOSED; concurrency tests in emulator + repository tests + UI conflict tests.
