# FINAL FEATURE CATALOG (Phase 0.75)

Scope: S-V1 owner-only (see PRODUCT-FREEZE + DECISION-REGISTER). Statuses: V1 REQUIRED · V1 OPTIONAL · DEFERRED · OUT OF SCOPE · UNVERIFIED.
Traceability: every feature is bound to screen(s) (see FINAL-SCREEN-CATALOG) and test contract (FINAL-TEST-CONTRACT). Behaviour shared with a reference feature is marked (F-## / BR-## / UC-##) and is VERIFIED source.

Field template per feature: Purpose · Actor · Roles · Preconditions · Screens · User actions · Business rules · Validation · Conditional logic · Database · Backend · Authorization · Notifications · Analytics · Audit · Error handling · Offline behaviour · Tests · Priority · Status.

Legend for the common (DB/auth/audit/offline) columns — these are defined once and referenced so the table stays readable:
- AUTHZ-OWNER = Firestore rule "request.auth.uid exists AND belongs to the org AND role is OWNER" (V1 single owner).
- AUD = owner-privileged action audited server-side (AuditLog) with actor/before/after/reason.
- DB-LOCAL/FIRESTORE/Room handled per OFFLINE-SYNC; every local write queues to outbox when offline.
- OFFLINE = queues locally, shows pending, idempotent replay; no fake success.

---

## A. Auth & lifecycle
### FV-1 Owner sign-in
- Purpose: authenticate the single owner to unlock cloud backup; offline entry continues after sign-in.
- Actor: Owner. Roles: OWNER. Preconditions: account provisioned server-side (N-07 path if first run).
- Screens: N-01 Splash, N-03 Login, N-07 Provisioning, N-08 Session-expiry, N-06 Disabled.
- User actions: enter email+password → Sign in; "Forgot password".
- Business rules: only OWNER-role account can open the app (V1). Single account.
- Validation: email format; non-empty password; server policy. Conditional: session cached → skip login; expired → re-auth preserving drafts/outbox.
- Database: users/{uid} (profile/role). Backend: Firebase Auth; CF for password reset; provisioning CF on first run. Authorization: AUTHZ-OWNER.
- Notifications: none (or security alert). Analytics: login_success/failure. Audit: auth events (server) only.
- Error: auth/network/rate-limit/disabled/expired → FINAL-ERROR-CONTRACT. Offline: allow cached-session auth (decision: enable) but first sign-in requires network; queue safe.
- Tests: FT-A1. Priority: P0. Status: V1 REQUIRED.

### FV-2 Password recovery / reset
- Purpose: owner self-serve password reset. Actor: Owner. Screens: N-04/05.
- Validation: email format; no enumeration; rate limit. Audit: account recovery. Tests FT-A2. P1 V1 REQUIRED.

### FV-3 Provisioning (first run / new account)
- Purpose: bind the single owner identity + org. Actor: Owner. Screen: N-07.
- Rules: owner identity = provisioning parameter (never hardcoded); creates org + users/{uid}=OWNER. Authorization: CF only. Audit: org/user created. P1 V1 REQUIRED.

### FV-4 Session-expiry handling
- Purpose: re-auth without losing drafts/outbox. Screen: N-08. Tests FT-A4. P1 V1 REQUIRED.

## B. Core daily workflow (owner)
### FV-10 Open/New Work Session
- Purpose: start a date+session (Morning/Evening) of work. Actor: Owner. Screens N-24→N-20/N-22.
- Rules: unique (date,session) in org; date valid; session per D-5. Validation incl. uniqueness (server). Conditional: existing open session → offer open/edit not duplicate. DB: workSessions. Backend: CF create (authoritative). Authz AUTHZ-OWNER. Audit AUD. Analytics work_session_created. Error: conflict/uniqueness. Offline queue. Tests FT-B1. P0 V1 REQUIRED.

### FV-11 Record Trip (add / next-trip copy)
- Purpose: record a trip with tractor, driver(record), place, notes, and labour+attendance. Actor: Owner. Screens N-25/N-23.
- Rules: tripNumber server/CF sequential across morning+evening per date, resets new date; copy-last-trip behaviour; driver/vehicle must be active catalogue; ≥1 present labour to enable save (attendance optional but present counts displayed). Validation cross-field. DB trips + attendance. Backend CF authoritative number. Authz AUTHZ-OWNER. Audit AUD. Analytics trip_created. Tests FT-B2. P0 V1 REQUIRED.

### FV-12 Attendance record
- Purpose: present/absent per labourer per trip, add/edit/remove labour with Undo, correction with reason. Actor: Owner. Screen N-23.
- Rules: labour in org catalogue; corrections carry reason; never silently overwrite history (ATTENDANCE-INTEGRITY). DB attendance (versioned) + roll-up counters. Backend CF for corrections/audit. Authz AUTHZ-OWNER. Audit AUD (before/after+reason). Tests FT-B3/FT-AUD. P0 V1 REQUIRED.

### FV-13 Dashboard / today
- Purpose: today's sessions/trips, search, add, quick summary. Actor Owner. Screen N-20. Rules per BR. Tests FT-B4. P0 V1 REQUIRED.

### FV-14 History + full search
- Purpose: browse grouped history, search across history. Screen N-27/history. Tests FT-B5. P0 (search) REQUIRED; full-history search S6 OPTIONAL.

### FV-15 Delete work/trip (soft)
- Purpose: remove trip/session with confirm + soft-delete + cascade + undo + audit + numbering invariant. Actor Owner. Screens N-20/22/23. Tests FT-B6. P0 V1 REQUIRED.

### FV-16 Close session
- Purpose: mark a day's session closed/immutable-ish. Screen N-22. Rules: owner only; audited; restricted correction window after close (D-8). Tests FT-B7. P1 V1 REQUIRED.

## C. Catalogues
### FV-20 Labourers catalogue
- Purpose: add/edit/soft-delete labourers (name, optional phone, active). Screen N-29. Tests FT-C1. P0 V1 REQUIRED.
### FV-21 Drivers catalogue (records)
- Purpose: add/edit/soft-delete drivers + optional vehicle. Screens N-31/N-30. Tests FT-C2. P0 V1 REQUIRED.
### FV-22 Tractors/vehicles (optional S2)
- Purpose: vehicle master for pickers. Screen N-30/Settings. P1 V1 OPTIONAL.

## D. Analytics & export
### FV-30 Analytics
- Purpose: KPIs + sortable data table (FR-14 parity). Screen N-27. Tests FT-D1. P0 V1 REQUIRED.
### FV-31 CSV export (S1)
- Purpose: export filtered/queried rows to CSV (owner, signed). Screen N-27. Tests FT-D2. P1 V1 OPTIONAL.

## E. Backup / DR
### FV-40 Secure local backup/restore
- Purpose: encrypted+signed `.labourbackup`; validate/rollback/verify. Screen N-38. Tests FT-E1. P0 V1 REQUIRED.
### FV-41 Cloud backup (owner account) optional
- Purpose: durable cloud copy/restore. Screen N-38. Tests FT-E2. P1 V1 OPTIONAL (owner confirms).

## F. Account/settings
### FV-50 Owner profile
- Purpose: name/phone/photo. Screen N-40. Tests FT-F1. P0 V1 REQUIRED.
### FV-51 Account & security settings
- Purpose: password, delete-my-data, data export, sign-out. Screen N-39. Tests FT-F2. P0 V1 REQUIRED.
### FV-52 Business settings
- Purpose: workType defaults, hints, flags. Screen N-37. Tests FT-F3. P1 V1 REQUIRED.
### FV-53 Help/About (S3)
- Screen N-42. P2 V1 OPTIONAL.

## G. Owner notifications (lite)
### FV-60 Owner notification centre (S4)
- Purpose: system events only (backup/restore/reminder). Screen N-41. No cross-role. Tests FT-G1. P2 V1 OPTIONAL.

## H. Security / audit (owner view)
### FV-70 Audit log viewer (S5)
- Purpose: owner-privileged action log (read-only). Screen N-36. Tests FT-H1. P2 V1 OPTIONAL.

---

## DEFERRED / OUT OF SCOPE (must NOT be implemented in V1)
| ID | Feature | Status | Gate |
|---|---|---|---|
| FV-80 Driver self-service home/trips/attendance | DEFERRED | D-1 |
| FV-81 Labourer self-service attendance | DEFERRED | D-1 |
| FV-82 ADMIN delegation/user mgmt | DEFERRED | D-2 |
| FV-83 Driver status workflow + assignment notify | DEFERRED | D-6 |
| FV-84 Cross-role notifications/announcements | DEFERRED | D-1/D-6 |
| FV-85 Legacy Hive/`.labourbackup` import | DEFERRED | D-3 |
| FV-86 Advanced/payroll reports | DEFERRED | D-7 |
| FV-90 Any web/desktop/biometric-PII/ML/ad feature | OUT OF SCOPE | product |

## UNVERIFIED (not relied upon in V1 spec)
- Any runtime performance/telemetry target, driver/labourer device usage, production dataset size.
