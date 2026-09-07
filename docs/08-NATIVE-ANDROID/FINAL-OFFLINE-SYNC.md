# FINAL OFFLINE / SYNC — V1 (Phase 0.75)

Per mutable operation: online/offline behaviour, local persistence, outbox, opId, retry, backoff, server validation, conflict, resolution, UI indication, final state. No fake success. Room = cache + outbox; Firestore = source of truth. Reference OFFLINE-SYNC-ARCHITECTURE + OFFLINE-STATE-MATRIX reconciled.

## Connectivity & sync states (app-wide)
Online · Offline · Stale(cached) · Syncing(n) · Sync-failed · Conflict · Recovered. Single top/banner chip; never colour-only (a11y).

## Per-op table
| Op | Online | Offline | Local persist | Outbox | opId | Retry/backoff | Server validate | Conflict | Resolution | UI indication | Final state |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Open session | CF direct | queue | session draft | yes | yes | yes exp backoff | uniqueness | duplicate/unique | offer open-existing/reload | queued→syncing→synced | open (synced) |
| Create trip | CF direct | queue | trip draft + roster | yes | yes | yes | active refs+number | number race | CF re-derive number | queued label + number hint | recorded |
| Attendance batch | CF | queue rows | rows | yes | per row/batch | yes | roster+reason | rev mismatch | keep saved/reload + reason | per-row pending / partial | saved/confirmed |
| Correction | CF (online req? queue ok) | queue | event | yes | yes | yes | reason required | rev | prompt | pending | corrected+history |
| Close session | CF online-preferred (blocked offline? decision: queue & replay online; show online-required note) | queue | intent | yes | yes | yes | status open | already closed | reload | pending/note | closed |
| Soft-delete (trip/session/catalogue) | CF | queue | delete intent | yes | yes | yes | owner+existence | already gone | reload | pending | deleted(soft)+audit |
| Catalogue create/edit | CF/owner rules | queue | record | yes | yes | yes | active/format/dup | rev | reload/keep | pending | saved |
| Settings | CF | queue | pending | yes | yes | yes | enum/range | rev | reload | pending | saved |
| Profile self | CF/rules | queue | record | yes | yes | yes | self fields | rev | reload | pending | saved |
| Backup/restore | online required | blocked w/ reason (local backup offline ok) | local file | yes | yes | yes | signature | overwrite-newer | confirm | progress | complete/fail+rollback |
| CSV export | online for cloud; local ok | local ok | file | — | yes | yes | range | n/a | — | progress | exported |
| Notifications read (S4) | online | queue read or on-online (decision) | mark | optional | yes | yes | self | n/a | — | pending | read |
| Auth/sign-in | online for 1st; cached-session offline allowed | cached-session | token | — | — | — | status/role | disabled | N-06 | — | home/signed-out |

## Rules (mandatory)
1. **No fake success**: a locally-only op is shown "Queued / pending-sync" until server ack; never rendered as server-saved.
2. Outbox replay in dependency order; later ops wait for earlier (number/session dependencies).
3. Retry: exponential backoff w/ jitter; stop flagging spam on permanent errors; offline ops auto-sync on reconnect.
4. Conflict: never silent last-write-wins where loss possible → owner prompt; server-derived values (numbers/status) resolved by CF.
5. Outbox is never silently dropped on sign-out/account switch; on disable/delete clear per consent/policy.
6. Session-expiry mid-queue: re-auth then reconcile; don't lose outbox.
7. Blocked-while-online-required ops (restore, 1st sign-in, cloud backup) show a clear reason, not a generic error.
8. Sync indicators show pending count and are actionable (retry/discard per item where allowed).

## Determinism
Each op maps to a typed local-outbox row with full payload + opId + dependency + state; repository replay is deterministic and unit/integration tested (FT-OFFLINE: offline write→reconnect→sync→no-fake-success, conflict, order, session-expiry, disable).
