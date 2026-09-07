# Backend Completeness

Status: Phase 0.5 (PROPOSED). Extends BACKEND-CONTRACT.md. Enumerates every required backend operation with actor, auth, authz, input, validation, read/write, transaction need, side effects, notifications, audit, response, errors, retry, idempotency. Detects ops a senior team would miss.

## Backend-op register (client-mutation ops, mostly CF-triggered)
| Op | Actor | Auth | Input | Validation | Transaction? | Side effects | Notify | Audit | Idempotent | Errors |
|---|---|---|---|---|---|---|---|---|---|---|
| BO-1 Create WorkSession | owner | yes | date,session,type | unique(org,date,session) | YES (uniqueness) | counter | none | create | yes (key) | conflict |
| BO-2 Create Trip | owner | yes | sessionId,trip fields | driver req; num unique | YES (number assign) | attendance scaffold | (assign notify D-6) | create | yes | conflict |
| BO-3 Record/update attendance | owner | yes | presence map | labour in roster | multi (partial) | roll-up counts | none | correction w/reason | yes (per op id) | validation |
| BO-4 Delete trip/session (soft) | owner | yes + confirm | id | reason? | YES (cascade) | attendance soft-delete; counter | none | delete w/reason | yes | conflict |
| BO-5 Session close | owner | yes | id | status=open | YES | counter final | none | close | yes | conflict |
| BO-6 Assign driver (D-6) | owner | yes | trip,driver | driver active | YES | trip.assignee | FCM driver | assign | yes | conflict |
| BO-7 Driver status update (D-6) | driver(own) | yes | trip,newStatus | state machine valid + own | YES | trip.status | FCM owner | update | yes | forbidden/conflict |
| BO-8 Crew CRUD (driver/labourer) | owner | yes | record | valid, active | no | — | none | create/edit | yes | conflict |
| BO-9 User invite/create (owner) | owner | yes | email,role,status | email unique; role allowed | YES | Auth user? invite email | email | create | yes | conflict |
| BO-10 Role/status change | owner | yes | uid,newRole | ≥1 active owner | YES | update user | notify user | role change | yes | forbidden/conflict |
| BO-11 Announcement send | owner | yes | title,body,audience | size | no | FCM topic | FCM | announce | yes | notif fail |
| BO-12 Backup (cloud) | owner | yes | scope | size | no | Storage+CF | completion notif | backup | yes | storage |
| BO-13 Restore | owner | yes | backup | version+rollback | multi | restore writes | completion | restore | no (one-shot id) | conflict/rollback |
| BO-14 Export report | owner | yes | period,filters | range | no | File/Storage | download notif | export | yes | storage/timeout |
| BO-15 Account data export | self | yes | uid | — | no | file | none | export | yes | storage |
| BO-16 Account delete/deactivate | owner/self(D-1) | yes re-auth | uid | guards (last owner) | YES | cascade? | notify | delete | one-shot | guarded |

## Backend needs a senior team may miss
| Need | Why | Severity |
|---|---|---|
| All writes server/CF-side for shared state | prevents client role/num/status tampering | CRITICAL |
| Idempotency keys on retried ops (BO-3/4/6/7/13) | retry duplication (see CONCURRENCY-AUDIT) | HIGH |
| Uniqueness enforced server-side (BO-1/2) | concurrent/offline duplicates | HIGH |
| ≥1-active-owner guard (BO-10/16) | lock-out | HIGH |
| Audit generated server-side (trusted) | client audit is forgeable (see AUDIT-LOG-SPEC) | HIGH |
| Notification delivery failure surfaced & retried server-side | BO-6/7/11/12 | MED |
| Counter/report aggregates maintained by CF | perf | MED |
| Denormalised displayName propagation on rename | consistency | MED |
| FCM topic/device mgmt & cleanup on role change | stale subscribers | MED |
| Payment/2FA decisions | out of scope; flag | LOW (decision) |

## Response/errors
Every op returns typed status + idempotent key. Client maps to ERROR-STATE-MATRIX. Offline ops routed via WorkManager outbox which replays idempotent BO-ops on reconnect (not raw SDK retries).

## Contract per op is documented in BACKEND-CONTRACT.md; this register is the completeness sweep. Any BO missing a screen trigger or a DB field is flagged in PRODUCT-GAP-REGISTER.
