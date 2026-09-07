# WORKFLOWS & STATE MACHINES — SAND WORKS

Business workflows + state machines. Every transition: who · precondition · validation · DB mutation · authorization · notification · audit · offline · retry · concurrency. No undefined state, no unnecessary states.

## 1. User lifecycle (driver/labourer)
```
Registered(pending) ─(OWNER approve via B-03)─► Active(approved)
     └─(reject)─► Rejected
Active ─(OWNER suspend/status)─► Suspended ⇄ Active
Active/Rejected/Suspended ─(account ops B-17)─► (status change/deactivate)
```
Expiry/revocation of elevated scope handled in Assignment machine. Enforced server-side (rules read approval+status each op).

## 2. Trip lifecycle
Driver records a trip for a date/time with tractor, driver(self), selected labourers, rateSnapshot, tripNumber(server), status. Trip status ∈ {recorded/draft, confirmed, corrected} — minimal:
```
Draft(unsaved) ─save(B-04)─► Recorded  (snapshot + number frozen)
Recorded ─(owner/driver edit permitted, B-05)─► Recorded(corrected)  [audited]
Trip is immutable once its daily closure runs (closure locks that day's trips).
```
No separate assigned/accepted/... driver workflow — directive makes drivers direct creators (§4/§9, SW-6). A trip not eligible at closure boundary stays as-is; closure snapshots eligible trips only.

## 3. Rate & money rule changes
```
Rate change(B-07): future-effective; historical trip rateSnapshots immutable.
Money-rule change(B-08): future-effective; closure uses snapshot active at closure.
```

## 4. Daily closure (authoritative)
```
Trigger (19:30 IST / owner-invoked / CF schedule)
 ─► validate no existing final closure for (org,date)
 ─► snapshot eligible trips + participants + rate/rule
 ─► compute per-user accrued (integer)           [B-09]
 ─► write earningCalculations + earnings (append, immutable)
 ─► write dailyClosures/{org}_{date} final       (idempotency boundary)
 ─► notify eligible users (A)
 ─► audit
Retry/idempotent: a 2nd run sees final closure → returns recorded result; no double money.
```
Never relabelled "payment". Owner can inspect calculation.

## 5. Labourer attendance / work-day tracking
Working/absent day derived/recorded per date. Corrections: OWNER only, reason + audit, append (no silent overwrite). Date-based history.

## 6. Temporary labour assignment
```
Create(B-11): labourerId, assignedBy, start, end, reason, scope, status=active, createdAt, expiresAt.
Active ─(now>expiresAt, server)─► Expired  (backend rejects elevated access)
Active ─(OWNER revoke)─► Revoked
```
No permanent escalation; expiry enforced by rules using server time.

## 7. Owner alert
Owner writes alert (B-13) → recipients → high-priority notification + in-app prominent alert UI + ack tracking. Honest Android limits (no forced volume; see NOTIFICATION-ALERT-SPEC).

## 8. Leaderboard
Recomputed (B-10) on closure/weekly/monthly boundary from real trips; top-3; deterministic tie; if fewer eligible → only real ranks.

## State-reaction guarantees
Every transition has defined success/failure/offline/retry/audit. Offline mutations queue idempotently; conflicts explicit; no fake success; no silent data loss/duplicates (OFFLINE-SYNC-SPEC).
