# CONCURRENCY, IDEMPOTENCY & AUDIT — SAND WORKS

Applies to a multi-device/multi-driver operation (multiple drivers + owner, offline). Definitions below make duplicates and double-earnings impossible and every privileged action auditable.

## Idempotency
- Every retriable mutation carries a client `opId`. Server/CF dedupes processed `opId`s (bounded window) → retry/offline-replay/CF-retry never double-applies. UI disables during Submitting; Retry reuses same opId.
- Daily closure is idempotent per `(orgId, date)` guard doc (exactly once; re-run returns recorded result). §38.

## Optimistic concurrency (rev)
- Editable docs (trip, rate change contexts, settings, labourer/driver/assignment, profile) carry `rev`. A write must match the read `rev` or is rejected → explicit Conflict (keep/reload/merge). Never silent last-write-wins on editable owner/driver data.
- Server-derived values (tripNumber, money, closure, leaderboard) resolve server-side via CF.

## Transactions
- Cross-doc invariants in CF Firestore transactions: trip create (number + snapshot + participants), closure (trips snapshot + earningCalculations + dailyClosures + earnings), approval→status+role, rate/rule change (settings + snapshot + audit), delete/correct cascade. Batched writes only where no read-modify-write dependency and atomicity on a write unit is the only need.

## Duplicate submission / multi-driver concurrency
- Two drivers creating trips on same day: tripNumber allocated in CF transaction (collision-safe). Duplicate tap prevented + idempotency.
- Two users editing same trip: rev conflict → explicit resolution.
- Repeated notifications (A–F, alert): server dedupe on eventId/alertId.

## Offline replay & ordering
- Room outbox replays in dependency order (trip before participants; assignment before use). Later op waits for earlier. No fake success; pending visible until server ack. Session-expiry/account-switch: outbox preserved per consent (never silently dropped).

## Audit logging (server-authoritative)
Audited events: rate change, money-rule change, trip create/edit, attendance correction, user approval, role/status change, temp assignment create/expiry/revoke, alert sent, daily closure, export, financial correction, account ops.
Audit fields: eventId(opId), orgId, actorUid, action, targetType/Id, prev/new (before/after), reason, createdAt(server), source(CF/schedule/manual), requestId.
Rules: audit written by CF/trusted backend only; client cannot write/delete auditLogs; owner read; append-only; retention per D-8; never log secrets/PII beyond needed.

## Alerts/notifications idempotency
Owner alert idempotent on alertId (no duplicate warnings on retry). Notification A–F deduped on eventId.

## Money safety
Closure idempotent; earnings append-only immutable; no double accrual; integer paise (no float); historical snapshots immutable. (MONEY-ENGINE-SPEC.)

## Tests
Double-submit, retry-same-opId, offline replay order, CF retry dedupe, rev conflict UI, trip-number race (multi-driver), closure run-twice→no double, notification dedupe, audit forge rejected, assignment expiry server-reject, no silent overwrite.
