# FINAL DATABASE SCHEMA — V1 (Phase 0.75)

Implementation-ready schema. Source: FIREBASE-DATABASE-DESIGN (Phase 0) reconciled to S-V1 owner-only + integrity/concurrency decisions. No field is unexplained; no feature lacks data. Every record carries: `orgId`, `createdAt`/`updatedAt` (server), `createdBy`/`changedBy` (server uid), `rev`, optional soft-delete `deletedAt`/`deletedBy`.

Global rule: **server/CF-authoritative** fields (see SERVER-AUTHORITY-MATRIX). Client never sets them.

## COLLECTION: organizations
- Purpose: single owner org root (V1 single org). Doc: fixed `orgs/{orgId}` (provisioned).
- Fields: `orgId`(P,req), `name`(req), `ownerUid`(req,server), `active`(bool), `createdAt`,`updatedAt`. Settings held in separate `settings`.
- Ownership/org: itself. Read: owner. Write: owner (display settings via CF). Delete: never hard (deactivate). Audit: creation/settings changes.
- Indexes: none (single).
- PII: business name only.

## COLLECTION: users (profiles)
- Doc: `users/{uid}` (1:1 auth).
- Fields: `uid`(P), `orgId`(server), `role`(OWNER in V1; enum reserved, server/CF-set), `status`(active/suspended; CF-set), `displayName`(client on profile, validated), `phone?`(opt, format), `email?`(masked UI), `photoUrl?`(opt), `createdAt`,`updatedAt`,`createdBy`.
- Read: self + owner. Update: self only name/phone/photo; role/status only via CF. Delete: deactivate (never hard) + CF account delete.
- Indexes: `orgId+role`, `orgId+status`. PII: yes.

## COLLECTION: labourers (catalogue)
- Doc: autoid `labourers/{id}`. Fields: `orgId`, `name`(req), `phone?`, `active`(bool), `createdAt/by`,`updatedAt/by`,`rev`,`deletedAt/by`(opt).
- Read: owner (V1). Write: owner via CF (or rules-owner for content) + audit on edits/inactivate. Delete: soft. PII: name/phone.
- Index: `orgId+active`.

## COLLECTION: drivers (catalogue)
- Doc autoid. Fields: `orgId`,`name`(req),`phone?`,`vehicleId?`(ref or text),`active`,`uid?`(V2 D-1 reserved),timestamps,`rev`. Read/write/delete as labourers. PII name/phone.

## COLLECTION: vehicles/tractors (optional V1 S2)
- Doc autoid. Fields: `orgId`,`name`(req),`active`,timestamps,`rev`. Owner CRUD.

## COLLECTION: workSessions
- Doc autoid. Fields: `orgId`,`date`(YYYY-MM-DD, req),`session`(enum morning/evening, D-5),`workType`(req, default Sand),`place?`,`status`(open/closed; owner/CF),`tripCount`(CF increment),`createdAt/by`,`updatedAt/by`,`closedAt?`,`closedBy?`,`rev`.
- Read: owner. Write: owner create/edit-while-open (CF for uniqueness & number & close). Delete: soft (CF cascade). Audit: create/close/edit.
- Indexes: `orgId+date`, `orgId+date+session`, uniqueness via CF (no single-collection unique index → enforced in transaction; recommended add a `sessions_meta/{orgId}_{date}_{session}` guard doc inside the create transaction).
- Sub: `workSessions/{id}/trips`.

## COLLECTION: workSessions/{id}/trips
- Doc autoid. Fields: `sessionId`,`orgId`(inherited),`tripNumber`(server/CF),`vehicleId?`,`driverId`(req),`place?`,`notes?`,`workType?`,`createdAt/by`,`updatedAt/by`,`rev`,`deletedAt/by`,`presentCount`,`absentCount`,`labourCount`(CF increment).
- Read: owner. Write: owner (content) via CF for numbering; delete soft via CF cascade. Audit.
- Indexes: `sessionId+tripNumber`, `orgId+driverId` (future), `orgId+deletedAt`.

## COLLECTION: workSessions/{id}/trips/{tripId}/attendance
- Doc key: `{labourerId}`. Fields: `orgId`,`tripId`,`sessionId`,`labourerId`,`status`(present/absent),`isPresent`(derived),`recordedBy`,`recordedAt`(server),`changedBy?`,`changedAt?`,`rev`,`removed`(bool soft),`removedAt?`.
- Read: owner. Write: OWNER record via CF audit; corrections append history (below). Delete: soft (removed) only.
- Indexes: for owner list by trip; worker-history query deferred (D-1).

## COLLECTION: attendanceHistory (append-only per correction) — integrity
- Doc autoid (or `{tripId}_{labourerId}_{eventId}`). Fields: `eventId`(opId),`orgId`,`tripId`,`labourerId`,`beforeStatus`,`afterStatus`,`reason`(req),`changedBy`,`changedAt`(server),`source`,`requestId`.
- Write: CF only. Read: owner. Never delete (retention D-8). Audit counterpart in auditLogs.
- Indexes: `tripId+labourerId`, `labourerId+changedAt` (future D-1).

## COLLECTION: notifications (V1-OPTIONAL owner system events)
- Doc autoid `notifications/{id}`. Fields: `userId`,`type`,`title`,`body`,`data{deepLink}`(nullable),`read`(bool),`createdAt`. Write: CF only (send). Read: self. Read/update read flag: recipient. Index: `userId+read+createdAt`. Retention 30 d (D-8). PII minimal.

## COLLECTION: notificationPreferences (optional)
- `userId`(P), channels enabled. Read/write self. Owner.

## COLLECTION: auditLogs
- Doc autoid. Fields per AUDIT-LOG-INTEGRITY: `eventId`,`orgId`,`actorUid`,`actorRole`,`action`,`targetType`,`targetId`,`previousState?`,`newState?`,`reason?`,`createdAt`,`source`,`requestId`.
- Write/delete: CF only (rules deny client). Read: owner. Retention 12 mo default (D-8). Index: `orgId+createdAt`,`orgId+actor+action`.

## COLLECTION: settings (org business config)
- Doc: `settings/{orgId}`. Fields: `workTypeDefault`,`workTypeOptions?`,`defaultVehicleHint?`,`sessionLabelDisplay`, `reportFlags?`, only confirmed items. Read: owner. Write: owner via CF; audit changes. `rev`.

## COLLECTION: backups (metadata) + Storage
- Meta doc autoid in `backups`: `orgId`,`type`(local/cloud),`version`,`size`,`sha256`,`encrypted`,`signed`,`url?`,`createdAt`,`createdBy`. Write CF. Read owner. Retention D-8.
- Files in Cloud Storage under `backups/{orgId}/{id}.labourbackup`, `exports/{orgId}/...`, `avatars/{uid}/...`. Storage rules owner/self only (see FINAL-FIREBASE-SECURITY-MODEL). No anonymous/public reads.

## Local (Room) store (offline-first)
- Mirrors Firestore records for cache + an `outbox` table of queued ops (`opId`, type, payload JSON, depGroup, createdAt, state). DataStore for prefs/draft. Room is cache+outbox, Firestore is source of truth; outbox replays idempotently. Draft autosave stored locally.

## Field/feature traceability guarantee
Every feature in FINAL-FEATURE-CATALOG reads/writes exactly the entities above. No orphan field; no feature needing absent data. New field = schema change request (IMPLEMENTATION-CONTRACT).
