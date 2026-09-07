# CONCURRENCY SPECIFICATION — V1 (Phase 0.75)

Closes the Phase-0.5 concurrency gaps for the V1 (single-owner) reality. Owner-only reduces concurrency to: multiple owner devices, offline replay, retry duplication, CF retries. Cross-role simultaneous edits are out of V1 but the revision scheme is built now so V2 is safe.

## 1. Idempotency
- Every retriable mutation carries a client-generated `opId` (UUID). Server/CF stores processed `opIds` per user (bounded window, e.g. 30 d) and returns the prior result on replay. Covers double-tap, network retry, offline replay, CF function retry.
- Mutations: create session, create trip, attendance batch save, corrections, deletes, close session, catalogue writes, settings, backup record, profile update.
- UI: submit buttons disabled while Submitting; Retry re-uses the same opId, never a new one.

## 2. Revision (optimistic concurrency)
- Editable documents carry `rev` (integer). Client writes must include the `rev` it read; CF/rules reject if current `rev` differs → CONFLICT.
- Applies to: trip (edits), attendance correction, labourer/driver/vehicle record, settings, workSession edit while open, profile (self).
- Server-only/append records (audit logs, notifications) need no `rev`.

## 3. Transactions / batched writes
- Firestore transactions (in CF) for cross-doc invariants: create session + uniqueness; allocate trip number (read max + write within txn); close session + final counter; delete trip + cascade attendance; correction + audit (single transaction to stay atomic); role/account changes.
- Batched writes acceptable where no read-modify-write dependency and atomicity on a multi-write unit is the only requirement (e.g., attendance batch rows) — but roll-up counters updated via Cloud Firestore `increment` (FieldValue) not client.

## 4. Duplicate submission (double-tap/retry)
- Prevent: disable during Submitting; idempotency key dedupe server-side; no duplicate workSessions/trips (R-01/R-10 uniqueness).
- UI test for double-tap Save/Delete/Backup.

## 5. Offline replay
- Each queued local operation stores: `opId`, type, payload, dependency group, timestamps. On reconnect replay in dependency order; a later op depending on an earlier one waits until earlier applied. On success clear; on CONFLICT route to resolution.
- No fake success: pending ops show "queued/pending-sync" until server ack.

## 6. Conflict detection & user-visible resolution
- Detect: `rev` mismatch on save; uniqueness violation; close-already-closed; restore-overwrites-newer.
- Resolution (owner): present "kept" vs "theirs" → keep mine / reload (use theirs) / (merge where meaningful). Attendance: prompt. Server-derived values (numbers, status) resolve via CF re-derivation; never silent last-write-wins where data loss possible. Document a default: **reload-and-keep-explicit-choice, never silent overwrite**.

## 7. Server authority
Values never trusted from client (see SERVER-AUTHORITY-MATRIX): role, ownerId/orgId, createdBy/changedBy, server timestamps, audit actor, sequence/trip numbers, status transitions authority, deletion, approval flags, active/last-owner guards, notification sends, backup signature.

## 8. CF retry duplication
- Cloud Functions are idempotent (eventId). A timed-out but succeeded function must not double side effects. Provide `opId`-based dedupe in CF.

## 9. Repetition under V1 single-owner
- Primary realistic races: same owner on two devices editing/saving offline simultaneously; reconnect flush racing a live edit. Handled by rev + uniqueness + idempotency. Documented tests: EDGE/CONCURRENCY scenarios.

## 10. Test mapping
FT-CONC: double-tap, retry-same-opId, offline replay order, CF retry dedupe, rev conflict UI, unique create race, close race. See FINAL-TEST-CONTRACT.
