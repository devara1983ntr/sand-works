# Firestore Database Design

Status: PROPOSED conceptual schema (Phase 0). No collections are created. Every proposed collection specifies owner, IDs, fields, indexes, lifecycle, PII, permissions, deletion policy, audit. Overlap with prior `FIREBASE-DATABASE.md` supersedes it.

## Conventions
- Every business record: `orgId` (owner org), `createdAt`/`updatedAt` (server timestamps where possible), `createdBy`/`changedBy` = `request.auth.uid`, optional soft-delete `deletedAt`/`deletedBy`.
- PII minimised; only fields actually required.
- Single-org in v1 (D-2), schema keeps `orgId` so multi-org is a future-safe index key without over-building.

## Collections

### `organizations` (1 per owner business) — DECISION: keep minimal for v1
- Purpose: org root/tenant + business settings (offline-first scope). Owner: OWNER.
- Doc ID: fixed single doc (e.g., `orgs/single` in v1) or autoid.
- Fields: `name`(str req), `ownerUid`(req), `displaySettings{...}`, `active`(bool), timestamps.
- Indexes: none for v1 single-org; `by ownerUid` if needed.
- Permissions: read OWNER/ADMIN/(members scoped); write OWNER only.
- Deletion: never hard-delete org while records exist (soft-deactivate). Audit: settings changes.

### `users` / `profiles`
- Purpose: identity + role + status + profile. Owner: the user (self) + org admin (role/status).
- Doc ID: `users/{authUid}` (1:1 with auth).
- Fields: `uid`(req), `orgId`(req), `role`(enum owner/admin/driver/labourer; req), `status`(active/suspended/invited; req), `displayName`(req), `phone`?(opt), `email`?(opt, masked in UI), `photoUrl`?(opt), `driverId`/`labourerId`?(opt link to catalogue), `createdAt`, `updatedAt`, `createdBy`.
- Indexes: `orgId+role`, `orgId+status`.
- PII: YES (name, phone, email). Minimise; phone only if needed for contact/login.
- Permissions: read self + OWNER/ADMIN (org); write self only limited profile fields (name/phone/photo) — role/status ONLY via CF. 
- Deletion policy: deactivate (never hard-delete org-linked), account deletion via CF w/ audit.
- Audit: role/status/email changes.

### `drivers` (catalogue)
- Purpose: master list of drivers (org-owned), referenced by trips. Owner: org/OWNER.
- Doc ID: autoid.
- Fields: `orgId`, `name`(req), `phone`?(opt), `vehicle`?(opt ref or text), `uid`?(opt link to user account, if D-1), `active`, timestamps, `createdBy`.
- Permissions: read OWNER/ADMIN (drivers? maybe all if driver sees roster—DECISION), write OWNER/ADMIN.
- Deletion: soft (keep historical trip refs). Audit on edits.
- PII: name/phone.

### `labourers` (catalogue)
- Purpose: master labour force (org-owned). Owner: org.
- Fields: `orgId`, `name`(req), `phone`?(opt), `uid`?(opt link if D-1), `active`, `dailyRate`?(opt, owner-only) — DECISION whether wage stored; timestamps.
- Permissions: read OWNER/ADMIN; write OWNER/ADMIN.
- Deletion: soft. PII: name/phone. Audit: edits.

### `workSessions` (mirrors Flutter `Work`)
- Purpose: a daily session unit (date + session morning/evening) an owner opens. Owner: org.
- Fields: `orgId`, `date`(req, as `YYYY-MM-DD`), `session`(enum morning/evening), `workType`, `place`, `status`(open/closed), `tripCount`, timestamps, `createdBy`.
- Indexes: `orgId+date`, `orgId+date+session`.
- Subcollection: `workSessions/{id}/trips`.
- Permissions: read OWNER/ADMIN (+ assigned drivers scope); write OWNER/ADMIN; close only by OWNER (or ADMIN if delegated).
- Deletion: soft only; a closed session is immutable-ish (audited).

### `trips` (subcollection `workSessions/{id}/trips`)
- Purpose: one trip in a session. Owner: org (creator records).
- Fields: `orgId`(inherited), `tripNumber`(req, server/CF-assigned), `tractorRef`/`vehicle`?(ref), `driverRef`(ref) OR `driverName`, `labourIds`?(denormalised), `status`(enum draft/planned/confirmed/completed/cancelled per D-6), `notes`, `place`, timestamps, `createdBy`.
- Indexes: by `sessionId+tripNumber`.
- Permissions: read OWNER/ADMIN + (assigned driver if self-service); write OWNER/ADMIN; driver status update via CF only.
- Deletion: soft; preserve numbering invariants.
- Audit: creation/status changes.

### `attendance` (or subcollection per trip)
- Purpose: per-worker per-trip participation/status. Owner: org + worker (subject).
- Recommend structure: `trips/{tripId}/attendance/{labourerId}` for easy per-trip query, plus roll-up counters on trip.
- Fields: `labourerId`, `status`(present/absent/confirmed), `isPresent`(bool derived), `recordedBy`, `confirmedByDriver`?(bool, if D-1), timestamps.
- Indexes: `labourerId+date` for a worker's history (requires labourer→date index; use a separate `labourerAttendance` view or query path).
- PII: links to labourer.
- Permissions: write OWNER/ADMIN (record); read worker own (if self-service); labourer cannot modify another's.
- Audit: changes/corrections by OWNER only.

### `notifications` (+ `notificationPreferences`)
- Purpose: user notification history + per-user preferences. Owner: recipient (history/prefs).
- Fields (notifications): `userId`, `type`, `title`, `body`, `data{deepLink}`, `read`(bool), `createdAt`.
- Fields (prefs): `userId`, enabled channels, quiet hours (optional).
- Indexes: `userId+read+createdAt`.
- Permissions: read/write self only; creation via CF/server only (never direct client broadcast).
- Deletion: retention window (D-8); read/unread update by owner.
- Audit: sends (server).

### `auditLogs`
- Purpose: admin/privileged action log. Owner: system.
- Fields: `orgId`, `actorUid`, `action`, `targetType`, `targetId`, `previousState`(opt map), `newState`(opt map), `reason`?(opt), `createdAt`(server).
- Permissions: write CF/admin only (rules deny direct client write); read OWNER/ADMIN.
- Retention: policy (D-8).
- Never log secrets.

### `settings` / business config
- Purpose: org-level business settings. Owner: org/OWNER.
- Fields: session boundary, work types, default tractor/driver hints, reporting flags (only confirmed).
- Permissions: read members (scoped), write OWNER.

### `notifications/readUnread`, `vehicles` optional
- Vehicle master optional (KEEP from reference: tractor picker).

## Relationships & indexes summary
org → users; org → drivers/labourers; org → workSessions → trips → attendance; users ↔ driver/labourer (optional link). Composite indexes per query patterns listed. Consistency via batched writes/transactions for numbering & counters. Denormalise only with documented propagation.

## Ownership feeding rules
`orgId` + `role` + creator/assignee/labourer fields drive every security rule (see `FIREBASE-SECURITY-RULES-DESIGN.md`).

## Retention / deletion policy (D-8; confirm, don't invent)
Active → archive (closed sessions) → soft-delete with audit → scheduled purge per policy. Notifications retention window. Uploaded files lifecycle. Legal/regulatory flags to business.

## Verification
PROPOSED only; no current Firestore schema exists (VERIFIED absence). D-1..D-8 control optional collections/fields.
