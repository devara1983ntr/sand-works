# FINAL BACKEND CONTRACT — V1 (Phase 0.75)

Every backend operation (Cloud Function / Auth / server) fully defined. Client never performs privileged ops. Reference Phase-0.5 BACKEND-COMPLETENESS + BACKEND-CONTRACT reconciled to S-V1.

Fields per op: Operation ID · Actor · Auth · Authorization · Input · Validation · DB reads · DB writes · Transaction · Idempotency · Side effects · Notification · Audit · Response · Errors · Retry.

V1 CF operations (owner). Marked (opt) where optional feature gated (S1/S2/S4/S5) — implement only if that feature is in the build.

## B-01 Provision org+owner (first run)
- Actor: new owner email; Auth: email verified; Authz: none→CF trusted (rate limited). Input: email, displayName, terms. Validation: email unique not already provisioned; name non-empty; prevent self-registration abuse (allowlist/invite token recommended; DECISION—default: provisioning by owner-initiated invite token or first-run secret). DB writes: organizations/{orgId}, users/{uid}=OWNER active. Txn: yes. Idempotent: keyed on email/uid. Side effects: welcome/security. Audit: org+user created. Response: ok+orgId. Errors: conflict/rate/validation. Retry: yes idempotent.

## B-02 Create/Open Work Session
- Actor OWNER; Auth yes; Authz owner+org. Input date, session, workType, place. Validation: date valid, session enum (D-5), uniqueness (date+session in org). DB reads: existing session guard; writes workSessions + `sessions_meta` guard doc. Txn: yes (uniqueness). Idempotent opId. Audit create. Errors: conflict/validation. Retry idempotent.

## B-03 Create Trip (number allocation)
- Actor OWNER. Input sessionId, vehicleId?, driverId, place, notes, workType?, labour list/attendance. Validation driver/vehicle/labour active+org; labour on roster. Txn: allocate tripNumber (max+1 within date across sessions) + write trip + attendance. Idempotent opId. Audit create. Errors: conflict/duplicate. Retry idempotent.

## B-04 Record / Correct attendance
- Actor OWNER. Input per-labour status (+reason when correction). Validation labour in trip roster/org; correction reason required. DB writes attendance(current)+attendanceHistory(append). Txn yes (history+current+audit). Idempotent opId per row/batch. Audit before/after+reason. Partial-failure semantics returned. Errors: validation/conflict(rev). Retry idempotent.

## B-05 Close session
- Actor OWNER. Input sessionId. Validation status==open; no concurrent close. Txn status=closed + final tripCount. Audit close. Conflict: already-closed. Retry idempotent.

## B-06 Soft-delete trip/session (cascade)
- Actor OWNER. Input id, reason(optional). Validation owner+org+existence. Txn soft-delete trip+cascade attendance OR session→trips. Audit delete. Undo supported at app layer (re-activate) until purge. Errors: conflict. Retry idempotent. Never hard delete (except governed purge D-8).

## B-07 Catalogue CRUD (labourer/driver/vehicle)
- Actor OWNER. Validation active/org/format/duplicate-warn. Audit edits/inactivate. Soft-delete. Idempotent.

## B-08 Settings update
- Actor OWNER. Validation enum/range; rev conflict; audit.

## B-09 Profile update (self)
- Actor self(owner). Authz self. Validation content; only self-allowed fields. Rules allow; CF optional for photo. Audit if identity-affecting.

## B-10 Account operations
- Auth: password reset, change password (self, re-auth). CF: delete-my-data (re-auth, guards, anonymize org refs, audit). Export my data (CF produce file). Suspend/activate OWNER (CF only, V2/D-2; in V1 owner cannot suspend self).

## B-11 Backup (local meta) / Cloud backup
- Actor OWNER. Input scope. Validation. CF coordinates encrypted+signed snapshot to Storage; write backups meta. Notification complete/fail (S4). Audit. Idempotent.

## B-12 Restore / Rollback
- Actor OWNER. Input backupId. Validation signature/version/checksum; confirm-over-newer. Txn multi-step: pre-restore snapshot → apply → verify → on mismatch rollback. Idempotent one-shot (requestId). Audit restore/rollback. Notification.

## B-13 Export CSV
- Actor OWNER (S1). Input period/filters. CF+Storage produce signed CSV. Audit. Errors storage/timeout.

## B-14 Send owner system notification (S4)
- Actor CF. Authz CF. Input type/data. Idempotent (eventId). Write notifications + FCM. Audit.

## Auth-side (non-CF)
- Sign-in (Auth), password reset (Auth), token refresh. Server enforces status/role at request time (custom claim or rules reading users.status). Disabled user denied.

## Per-operation guarantees
Auth · Authz (owner+org / self) · Validation server-side · Transaction where cross-doc · Idempotency (opId) everywhere retriable · Audit server-written · Notification only where defined · Response typed (maps to FINAL-ERROR-CONTRACT) · Retry idempotent. Missing ops not in V1 (driver workflows) are NOT implemented (D-1/D-6).

## Function boundary (what lives where) — see FINAL-FIREBASE-SECURITY-MODEL §Function-Boundary
UI concern → Android; simple authz/field validation → Firestore Rules; trusted privileged/business op → CF (B-01..B-14). Direct reads (queries) → Rules only; no CF wrapper on every read.
