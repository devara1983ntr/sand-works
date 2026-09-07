# Screen-by-Screen Specification

Status: Phase 0.5 (PROPOSED). Uses the §5 template for the most important native screens. Screen IDs from COMPLETE-SCREEN-INVENTORY.md. States defined per screen; full state matrices in SCREEN-STATE-MATRIX.md. Decision flags D-1..D-8.

## N-01 Splash / Launch gate
- Purpose: decide where the user lands after start.
- Primary user: all. Roles: all. Auth required: n/a (check). Authz: n/a.
- Entry: cold start, process recreation, deep link. Exit: N-03 (no session), N-20/50/60 (session + role), or N-08/N-06 (expired/disabled), or directly to a deep-linked screen after auth.
- Route: `splash`. Deep-link: if launched by a link, resolve after auth.
- App bar: none. Back: exits app. Bottom nav: none.
- Primary CTA: none (auto). Content: brand, progress.
- Loading: check auth state. Offline: if no session + offline → cannot sign-in (unless we adopt offline-cached auth; decision) → show retry.
- States: Initial, CheckingAuth(Loading), Authorized, Unauthorized, Offline.
- A11y: brand described; no decorative timer needed (reference used fixed 1.5s — improve to readiness-gated).
- Tests: launch→auth; deep-link auth gating.

## N-03 Login
- Purpose: sign in (email/password v1; phone if D-1).
- Roles: all. Auth: yes.
- Fields: email/identifier (required, email format), password (required, min policy). Options: reset password, (sign-up not public unless invited). 
- Validation: client + server (Auth) both. Cross-field none.
- Loading: submit spinner; disable double-submit. Error taxonomy: invalid credentials, disabled, network, session.
- States: Initial, Submitting, Success→(role route), Error(message), Disabled(N-06), Offline(retry), Unauthorized.
- Behaviour: after success resolve role → N-20/50/60; on first login w/o profile → N-07 provisioning.
- Security: no account enumeration messaging; no credential logging; App Check.
- Tests: happy, wrong password, disabled, offline, double submit.

## N-20 Owner Dashboard
- Purpose: today summary + quick actions (reference S-02).
- Roles: OWNER/ADMIN.
- Content: date/session selector, summary cards (morning/evening/trips), current-session trip counter, quick "Record trip"/"New work", recent trips, notifications badge.
- Primary CTA: record trip / open today's work. Secondary: view history/reports, manage crew.
- Destructive: delete trip (confirm + audit) — owner only.
- Search: today (driver/trip) → optionally full history (see SEARCH).
- Loading skeleton; Empty (no work today) with CTA; Error w/ retry; Offline (cache + offline indicator); stale.
- Gestures: pull-to-refresh (online), tap trip→N-23, swipe-to-delete (confirm+undo).
- A11y: semantics, 48dp, contrast.
- Backend/data: workSessions/trips read (rules owner). 
- Tests: dashboard states; role; offline.

## N-23 Trip detail + attendance editor
- Purpose: view trip + record/confirm attendance (reference S-07).
- Roles: OWNER/ADMIN (record). If D-1 driver may read own/confirm via CF; labourer read own.
- Content: trip info, labour roster, per-person presence toggles, add/remove labour (owner).
- Validation: ≥1 labour; labour belongs to roster; attendance transition rules (owner records; corrections audited).
- Destructive: remove labour (owner, undo); delete trip (owner, confirm+audit) — not from here typically.
- States: Loading, Loaded, Empty(no labour), Error(retry), Offline(queued writes shown pending), Submitting, Forbidden(non-owner edit denied).
- Conditional: driver/labourer see read-only/confirm-only UI.
- Backend: attendance writes via rules/CF; audit on correction.
- Tests: owner records; labourer cannot edit another; offline attendance queued.

## N-24 New/Edit Work Session & N-25 New Trip/confirm
- Purpose: create/edit a day session and add a trip (reference S-08/S-09).
- Roles: OWNER/ADMIN.
- Validation (form spec): date+session unique; work type required; ≥1 labour to save a trip; driver required for trip; cross-field (session boundary); duplicate detection (existing session).
- Draft autosave (reference behaviour kept).
- Destructive: discard/back with unsaved changes → confirm; deleting a trip on edit.
- States: Initial(fields), Loading(existing session), Submitting, Success→return+N-20 refresh, Error, Conflict(session changed by other), Offline(queued, pending), ValidationError.
- Conditional: editing preserves id/tripNumber/date/session; trip numbering server-authoritative.
- Backend: workSessions/trips create/update (rules/CF number), audit.
- Tests: create, edit-preserve, validation, duplicate, offline, conflict.

## N-27 Reports
- Purpose: KPIs + tables + export (reference S-05/analytics).
- Roles: OWNER/ADMIN; driver limited own (if D-1).
- Content: period filter, KPIs (works/trips/labour/labour-days), driver & labourer summaries, export.
- Search/filter/sort per REPORT list.
- Empty/Error/Offline.
- Backend: aggregate queries; expensive reports via CF/scheduled (avoid client full-history load — perf finding PERF-2).
- Tests: KPIs, role-scoped data, export, empty.

## N-33 Users & roles management (owner)
- Purpose: create user accounts (invite), assign/change role, suspend/activate, delete. Owner/ADMIN (role changes owner/CF).
- Fields/validation; destructive (suspend/delete) requires confirmation + recent auth; audit.
- States: Loading, Loaded, Empty(no users), Error, Submitting, Forbidden(non-owner cannot change role), Offline(read cache; writes online or queued? role mgmt server-authoritative → online).
- Backend: only CF changes role/status (client cannot). Audit log entry.
- Tests: owner adds user; driver attempts change role → DENY; suspended user can't sign in.

## N-36 Audit log viewer
- Purpose: review privileged actions.
- Roles: OWNER/ADMIN read.
- Content: filter by actor/action/date; read-only; never show secrets.
- Empty/Error/offline(optional).
- Backend: auditLogs read (owner/admin); writes server-only.

## N-38 Backup & restore
- Purpose: export/import data + cloud backup (reference S-06).
- Roles: OWNER.
- Content: local `.labourbackup` export (encrypted/signed), cloud backup status, restore w/ validation+rollback+verification.
- Destructive: restore overwrites → confirm + snapshot rollback.
- Offline: local export works; cloud requires online.
- Tests: backup, restore success/rollback, invalid file, permission, offline.

## N-40 Profile view/edit
- Roles: all. Fields: displayName, phone, photo (not role/email/status). 
- States: standard.
- Validation: self only limited profile; role/status immutable from client.
- Tests: edit own; cannot change role.

## N-41 Notification centre
- Roles: all. Lists own notifications; read/unread toggle; deep link to origin (re-authorized).
- Empty/offline/error. Backend: notifications read self; server creates.
- Tests: list, mark read, deep-link, unread count, empty.

## N-42 Help / About
- Roles: all. Version, contact/support (if any), privacy policy link, glossary. Not core; REQUIRES DECISION on content.

## State/other screens summarized
N-50..N-53 driver, N-60..N-63 labourer, N-22 day detail, N-26 attendance overview, N-28 report detail, N-29..N-34 crew/users mgmt, N-35 announcement manager, N-37 settings, N-39 account/security. Each follows the same template at implementation; role gating per RBAC-DESIGN.md.

## Verification
All EXISTING reference screens covered; NEW screens justified; role screens pending D-1; full per-screen state matrices in SCREEN-STATE-MATRIX.md.
