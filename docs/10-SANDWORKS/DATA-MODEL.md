# DATA MODEL — SAND WORKS

Single org (family). Every business record: `orgId`, `createdAt`/`updatedAt` (server timestamps), `createdBy`/`changedBy` (= request.auth.uid), optional soft-delete `deletedAt`/`deletedBy`, `rev` where concurrency needed. Immutable historical snapshots where required. Firestore is authoritative; Room = local cache + outbox for offline.

Server-authoritative (never client-trusted) per §23: role, approval status, money, rate, trip numbering, leaderboard, authorization, daily closing, audit records, orgId, timestamps, createdBy/changedBy.

## Core entities / collections
| Collection | Doc ID | Key fields | Notes |
|---|---|---|---|
| organizations | fixed single (orgId) | name, ownerUid(server), active, timestamps | single family org; owner = Ramesh Sahu |
| users | {uid} | uid, orgId(server), role(OWNER/DRIVER/LABOURER; server), status(active/suspended), approval(pending/approved/rejected for D/L; server), displayName, phone?, photoUrl?, driverRef?/labourerRef?, timestamps | 1:1 auth; role/approval/status only via CF |
| drivers | autoid | orgId, name, phone?, tractorRef?(default), active, uidRef?(link to users), stats?, timestamps | owner-managed; approved user linked |
| labourers | autoid | orgId, name, phone?, photoUrl?, active, uidRef?, dailyRate? (config), timestamps | owner-managed; approved user linked |
| tractors | autoid | orgId, name(Sonalika/John Deere initial), active, timestamps | OWNER-managed registry (NOT hardcoded logic) |
| trips | autoid (sub by date or flat w/ indexes) | orgId, date, time, tractorRef, driverRef(or driverId), labourerIds[], status, tripNumber(server), rateSnapshot(amount,currency, ruleRef), calculatedTotal, note?, timestamps, rev | tripNumber CF-authoritative collision-safe; rate snapshot immutable per trip |
| tripParticipants | per (trip,user) or embedded | orgId, tripId, userId, role(driver/labour), eligible/status | distribution membership; present/eligible |
| attendance | per (date,user) OR derived from trips | orgId, date, userId(role), status(working/absent), derived | working/absent day tracking; history date-based; corrections owner+reason+audit |
| earnings | per (user,date) | orgId, userId, date, role, accruedAmount(integer paise), breakdown, calcRef | accrued totals; appended, immutable; wording never "payment" |
| earningCalculations | per closure | orgId, date, inputs(snapshot trips/rates/participants), outputs(per-user amounts), rule used, status, createdAt | auditable calculation record; deterministic; input+output preserved |
| dailyClosures | {orgId}_{date} | orgId, date, status, calcRefs, closedBy, closedAt, rev | idempotency boundary = (org,date); exactly once |
| assignments (temp labour) | autoid | orgId, labourerId, assignedBy(owner/driver), startDateTime, endDateTime, reason, scope, status, createdAt, expiresAt, rev | expiry backend-enforced |
| notifications | {userId}-... | userId, type(A–F), title, body, data(deepLink), read, createdAt | CF send; per-user; retention |
| alerts | autoid | orgId, senderUid(owner), message?, recipients[], sentAt, ack(userId,at), priority | owner-only; operational warning |
| leaderboards | {scope}_{period} derived | orgId, type(weekly/monthly), periodStart, ranks[{userId, rank, totalTrips/amount}], tieRule | derived from real trips; top-3; recompute on closure/change |
| auditLogs | autoid | orgId, actorUid, action, targetType/Id, prev, new, reason?, createdAt(server), source | CF-only write; owner read; append-only |
| settings | {orgId} | rates{default,current}, moneyRule, summaryTime(19:30), tractor registry refs, notif prefs, etc. | owner write via CF; rev |
| rateSnapshots | autoid | orgId, rate(paisa), effectiveFrom, setBy, audit | history of rate; trips reference snapshot |
| moneyRuleSnapshots | autoid | orgId, rule(equal/driver+labour/custom%), params, effectiveFrom | immutable rule per calc |

## Immutability & snapshots
- Trip holds an immutable `rateSnapshot`; changing future rate never alters historical trip totals (§10).
- `earningCalculations` + `dailyClosures` append-only and auditable.
- Earnings are accrued totals (integer paise), immutable after closure; never relabelled as payment (§13).
- Attendance corrections append (no silent overwrite) with reason + audit (§16).
- Money calculations preserve inputs+outputs; use integer smallest currency unit (paise) — no floating-point error (§11).

## Relationships & indexes
org → users/drivers/labourers/tractors/trips/attendance/earnings/closures/leaderboards/notifications/alerts/audit/settings. Queries: driver own trips by date; labourer own attendance/earnings by date range; leaderboard period; owner org-wide aggregates (via derived counters/CF, not full scans). Indexes defined per query in implementation control; **every query must be rule-compliant** (Security Rules are not filters) — see SECURITY-RBAC.

## Local (Room) store
Cache mirror + outbox (opId, type, payload, depGroup, state). DataStore for prefs/settings cache. Offline writes queue idempotently; no fake success; conflicts explicit.

## Retention/PII
Family/private data; PII = names/phones/photos. Photos only if Blaze + Storage rules (size/MIME/dimension, authenticated, no public). Retention defaults per D-8; confirm exact window (non-blocking). No analytics PII.
