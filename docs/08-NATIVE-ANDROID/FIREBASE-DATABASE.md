# Firestore Database Design (recommended)

Status: PROPOSED design spec. Fields marked "proposed" are recommendations; nothing exists in the current repo. Commit audited `2dd2fe4`. Map each current Hive collection (`DATABASE.md`) to a Firestore collection while adding ownership/audit.

## Conventions
- IDs: Firestore auto-IDs or deterministic per domain; store `createdAt`, `updatedAt` (server timestamps where possible), `ownerId` (the org/owner), `createdBy`/`changedBy` (auth.uid), soft-delete flag `deleted:boolean` where needed.
- No client-trusted role fields as sole authorization; roles in user doc + custom claims.
- Data minimisation: only fields actually required.

## Proposed collections (each with required/optional/ownership/rules/audit)
### `organizations` (orgs/tenants)
Purpose: top-level owner scope. Owner: org admin. Read/write: members by role (rules). Required: name, ownerUid, createdAt. Optional: settings. Indexes: by ownerUid. Security: only owner/admin manage; members read.
Audit: on config change.

### `users` / `profiles`
Purpose: identity+role+profile. Readable by: self (full), others (limited), admin. Writable by: self (limited profile), admin/CF (role/status). Required: uid, role, status(active/suspended), displayName, createdAt. Optional: phone, profileImage, orgId. Indexes: by orgId+role, by status. Audit: role/status changes.

### `jobs` / `works`
Purpose: a unit of work (mirrors `Work`, plus optional assignment to a driver & job status). Owner: organization/owner. Readable by: members by role (owner all; driver assigned; labourer if linked). Writable by: owner (create/update status via rules/CF); driver limited status. Required: orgId, workType, session, date, assignedDriverId?, status, place, createdBy. Optional: notes. Indexes: orgId+date+status; orgId+assignedDriverId+status. Audit: status & assignment transitions.

### `trips` (optional if job-centric; or fold into jobs)
If kept as separate child: each `trip` under a job/work (subcollection `jobs/{id}/trips`). Required: tripNumber, tractorId, driverId, jobId, status, createdBy. Optional: notes, place. Indexes: jobId+tripNumber. Audit: status changes.

### `tractors` / `vehicles`
Optional master list owned by org. Required: name/plate. Audit on edits.

### `labourers` / `crew`
Mirrors `Labour` master; add orgId, active, wage details (owner-only). Optional: phone, dailyWage, bank (if payments). Indexes: orgId+active. Audit: wage/status changes.

### `attendance`
Per worker per date/trip. Required: orgId, labourerId, date, session, tripId, status(present/absent), recordedBy. Writable: owner/admin and (with authorisation) authorised input; labourer read own. Indexes: labourerId+date; tripId. Audit: corrections by admin only. (Mirrors `TripLabour`.)

### `notifications`
System/user notification history. Required: userId, type, title/body ref, read, createdAt, deepLink. Indexes: userId+read+createdAt. Writable: server/CF only. Audit on send.

### `auditLog`
Admin/privileged action log. Required: actorUid, action, targetType, targetId, timestamp, metadata. Writable: server/CF only. Retention: policy-defined.

### `backups` / media (Storage)
Purpose: authenticated export/media. Rules per role; owner writes/reads; App Check.

## Relations summary
org → users; org → jobs → trips → attendance; org → labourers; users(role) authorize reads/writes by rules. Mirrors current model but with owner scope & audit.

## Consistency & transactions
- Use batched writes/`runTransaction` for multi-doc invariants (trip numbering, attendance + job counters, role+profile updates).
- Denormalise only where query patterns justify (e.g., labourer displayName on attendance) with documented propagation; avoid over-denormalization.

## Retention / lifecycle
- Soft-delete for audit & undo; hard-delete only server-side with hold checks.
- Retention policy per collection (audit, notifications).

## Mapping from current Hive
Hive → Firestore: `work_box`→`jobs/works`(+org); `trip_box`→`jobs/{id}/trips`; `labour_box`→`labourers`; `trip_labour_box`→`attendance`; `draft_box`→ local draft only (Room/DataStore, not Firestore).

## Findings (from current product) that shape design
- No auth/ownership today → add orgId + createdBy everywhere.
- Attendance currently loses history (latest state only) → design versioned/audited attendance.
- Deterministic work-id ambiguity → use server timestamps/auto-ids.
- Trip `status` unused → define an explicit job/trip status lifecycle.

## Verification status
PROPOSED only; no current Firestore database exists (VERIFIED absence in repo).
