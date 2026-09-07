# BACKEND OPERATIONS & QUERIES — SAND WORKS

Server-authoritative operations (Cloud Functions when Blaze; else documented). Client never performs privileged ops. Every op: caller · auth · authorization · input · validation · transaction/concurrency · idempotency · output · errors · audit · offline behaviour. Rules are not filters → every query rule-compliant; indexes declared.

## Cloud Function operations (B-)
| ID | Op | Caller | Authz | Key validation / server-authority | Idempotency | Audit |
|---|---|---|---|---|---|---|
| B-01 | Provision owner org (first run) | new owner | none→CF | org + users/ownerUid=OWNER; never self-role-choice | email key | org/user created |
| B-02 | Register driver/labourer | new D/L | self | creates approval=pending; role fixed D/L | opId | registration |
| B-03 | Approve/reject user | OWNER | owner | sets approval; role; active | opId | approval |
| B-04 | Create trip | OWNER/DRIVER | owner; driver own | **tripNumber CF-authoritative**; rate snapshot; tractor/labour valid | opId | trip create |
| B-05 | Edit trip (permitted) | OWNER/DRIVER | owner; driver own+window | status/fields; immutability of number/snapshot | opId+rev | trip edit |
| B-06 | Attendance/correction | OWNER | owner | reason required; no silent overwrite | opId | correction |
| B-07 | Rate change | OWNER | owner | rateSnapshot effectiveFrom; future-only | opId | rate change |
| B-08 | Money-rule change | OWNER | owner | rule snapshot; future-only | opId | rule change |
| B-09 | Daily closure/summary | scheduled/owner/CF | owner/CF | per (org,date); exactly once; integer calc | closure guard | closure |
| B-10 | Leaderboard compute | CF/closure | owner/CF | from real trips; deterministic tie; top-3 | per period | (derived) |
| B-11 | Temp assignment create | OWNER/(auth driver) | owner/scope | endDateTime/expiry; scope | opId | assignment |
| B-12 | Temp assignment expiry/revoke | CF/OWNER | CF/owner | status; backend reject on expiry | opId | expiry |
| B-13 | Send alert (owner warning) | OWNER | owner | recipients; strongest-compliant; ack tracked | alertId | alert sent |
| B-14 | Notification send (A–F) | CF | CF/scope | per-user targeting; no private cross-broadcast | eventId | (notify) |
| B-15 | Export PDF/CSV | OWNER | owner | range/breakdown; real data; storage(Blaze) | requestId | export |
| B-16 | Profile photo upload (Blaze) | self | self | size/MIME/dimension; signed URL | — | — |
| B-17 | Account/status ops (owner) | OWNER | owner | suspend/status; audit | opId | account |
| B-18 | WhatsApp/share (driver) | DRIVER | driver | client share intent; real count | — | — |

## Server-authoritative values (client never trusted)
role · approval · status · orgId/ownerUid · tripNumber · rateSnapshot · money/totals · distribution · closure · leaderboard · timestamps · createdBy/changedBy · audit · alert sender · expirations. Client writes only validated domain content + opId.

## Representative queries (must be rule-compliant; indexes declared)
| Query | Actor | Filters | Rule predicate |
|---|---|---|---|
| Own dashboard metrics | D/L | role+uid+org | `uid==mine && org==mine` + approved/active |
| Today's trips (owner org / driver own) | O/D | org / +driverId | org equality + scope |
| Tractor totals by date | O | org+date | org equality |
| Labourer history | L | uid+org+date range | labourerId==mine |
| Leaderboard period | O/D/L | org+period | org equality (top-3 derived) |
| Pending approvals | O | org+approval=pending | owner only |
| My earnings summary | D/L | uid+org | uid==mine |
Every query includes the rule-matching field (org/uid/owner) + status/approval/active; indexes added per FINAL-QUERY pattern; no unbounded scans. Deep-link/notification reads re-validate existence+ownership.

## Spark-vs-Blaze (operations)
- B-16 (Storage photos), B-15 cloud export, B-09 scheduled, B-14 FCM all require Blaze. On Spark: implement owner-invoked/documented fallbacks; never fake (see SCHEDULING-SPEC + DIRECTIVE-REGISTER SW-BLK-2).
- Offline writes queue idempotently; sync replay via WorkManager calling these ops with opId; conflicts explicit.

## Tests
Each B-op: emulator CF test (authz/validation/txn/idempotency/audit) + rule test for every query; escalation + correctness matrix (TEST-AND-QUALITY-SPEC).
