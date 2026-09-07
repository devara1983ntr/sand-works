# ATTENDANCE INTEGRITY — V1 (Phase 0.75)

Fixes the reference defect (audit: attendance history lost on overwrite). Attendance is an **append-immutable audit trail**, never a silent in-place rewrite.

## Core requirement
Historical attendance records are immutable once written. A change is a **correction** that records: original value, corrected value, actor, timestamp, reason, and an approval flag (V1: OWNER is both actor+approver). UI may show the current value and a change history; the raw historical record is never deleted/overwritten.

## Write model
- Storage: `trips/{tripId}/attendance/{labourerId}` holds the **current** record with `history` optional inline OR an append-only subcollection/`attendanceEvents`. Decision: store current record + an append-only `attendanceAudit/{...}` event per change for queryability; do NOT store unbounded history inside the single attendance doc.
- Every attendance record: `labourerId`, `orgId`, `tripId`, `sessionId`, `status` (present/absent), `isPresent` (derived bool), `recordedBy` (uid), `recordedAt` (server), `rev`.
- Every correction event: `eventId`(opId), `tripId`, `labourerId`, `beforeStatus`, `afterStatus`, `reason`(required, non-empty), `changedBy`(uid), `changedAt`(server), `source`.

## Correction rule (R-21/R-33)
- Applies when changing a record that is part of a closed session or previously confirmed/completed attendance, AND any change to a present/absent value after initial recording within an open session also records a correction reason if the labourer had already been "confirmed/settled". Decision: **require `reason` for all corrections after the first save**; initial record during open-session data entry does not require reason (it's original capture), but any change to an already-saved record DOES require reason. Simpler and auditable.
- Only OWNER may correct in V1 (AUTHZ-OWNER + CF audit). Labourer/driver self-confirm deferred (D-1).

## Chain of custody
recordedBy → correctedBy(actor) → audited via event. `recordedBy`/`changedBy` are server-set to `request.auth.uid` — never client-declared.

## Immutability of history
- No hard delete of attendance records. Soft-mark "removed" (labour removed from trip) preserves the historical record and shows the labourer was on that trip.
- Deleting a whole trip/session cascades soft-delete of its attendance but keeps the event/audit trail (a correction/deletion audit remains) per R-30/31.
- Roll-up counters (`presentCount`, `absentCount`, `labourCount`) on the trip are derived/`increment`-updated and reconciled by CF — not authoritative history.

## UI (N-23)
- Owner toggles status; on a saved record change the UI requests a **reason** (required) and shows a confirmation; after save shows the updated count + a "history/audited" hint; partial-failure keeps saved rows + flags failed with Retry (opId).
- A "corrections/recent changes" viewer (N-23 or N-36) shows before/after + reason + actor + time.

## Approval
V1: OWNER is both actor and approver (no separate approver). `approval` field reserved for V2 (driver/labourer self-confirm D-1) — do not build approval workflows now.

## Authorization
- Write record/correct: OWNER via rules + CF audit. Read: owner (org) in V1; subject-read deferred (D-1).
- Rules must not allow a plain client write that bypasses the correction/reason/audit path — corrections route through CF.

## Audit trail (see AUDIT-LOG-INTEGRITY)
Each correction emits an audit event (before/after/reason) server-side. Retention per D-8.

## Verification
Tests: FT-AUD/FT-B3 (initial record, reason-required correction, soft-remove preserves history, no silent overwrite, cascade soft-delete keeps trail, rev conflict). See FINAL-TEST-CONTRACT.
