# Form & Validation Specification

Status: Phase 0.5 (PROPOSED). Every form: fields, type, required/optional, default, validation, format, cross-field rules, keyboard/IME, focus, error, server re-validation, duplicate detection, unsaved handling, submit/retry/reset/cancel. Client validation is UX; **server/rules/CF validation is authoritative**.

## F1 Login (N-03)
Fields: identifier (email; required; format), password (required; policy). Defaults none. Validation: required, email format, non-empty password. Cross-field none. Keyboard: email/text, IME done→submit. Error: "email/password incorrect" (no enumeration), disabled message, offline. Server: Auth validates. Submit disable-double. Reset/cancel links.

## F2 Provisioning / first-time profile (N-07)
Fields: displayName (req), phone (opt), photo (opt), accept terms. Server assigns role/org. Validation: name non-empty; phone format if provided. Cross-field: photo size/type. IME. Error states. Must NOT allow choosing own role.

## F3 New/Edit Work Session (N-24)
Fields: date (required, picker, format yyyy-MM-dd), session (Morning/Evening; boundary D-5), workType (required), place (opt), status.
Validation: date present & not absurd future? (D-8), session allowed, uniqueness (org,date,session). Cross-field: date+session unique; workType default "Sand (Bali)" reference? (reference seeded that; keep as default but allow change).
Client inline; **server enforces uniqueness via transaction/CF**. Draft autosave on dirty. Submit→save. Duplicate detection → offer open-existing or change.

## F4 Trip form (N-25)
Fields: tripNumber (auto, read-only, server-authoritative), tractor/vehicle (picker from catalogue; default from last), driver (picker; required), place (opt), workType, notes (opt), status (default by D-6), labour list (≥1 present to enable save).
Validation: driver required; ≥1 labour; attendance within roster; tractor/vehicle/driver must be active catalogue entries (cross-field). Client + server. IME. Autosave draft. Duplicate: tripNumber collision prevented server-side.

## F5 Attendance (N-23)
Fields: per labourer presence toggle; (add labour via picker from labourer catalogue; or quick add name).
Validation: labour must exist in org (catalogue); cannot toggle a labourer removed from roster; correction reason (if editing completed/confirmed) required (owner).
Server: rules/CF enforce who may write; corrections audited with reason. Partial-failure handling.

## F6 Crew (driver/labourer) add/edit (N-29..32)
Fields: name (req), phone (opt, format), active (toggle), vehicle (driver), uid-link (optional, D-1).
Validation: name non-empty; phone format; duplicate name warning; active required if assignable.
Server: org catalogue rules (owner); soft-delete; audit.

## F7 User/role management (N-33/34)
Fields: email (new user; req), role (owner/admin/driver/labourer; req), status, link to driver/labourer.
Validation: role allowed; cannot demote/remove last active owner; email uniqueness; invite.
Server: **only CF** changes role/status; audit; requires owner + recent re-auth for destructive.
Cross-field: role=driver ⇒ optional driver link; role=labourer ⇒ labourer link.

## F8 Profile (N-40)
Fields: displayName, phone, photo. Not editable: role, email (email via account settings/N-39 with verification), status, org.
Validation self-limited; server rules allow only permitted fields (field-level security).

## F9 Account/security (N-39)
Change password, email (re-auth), request data export/delete, enable 2FA (decision), notification prefs.
Validation: current password; policy; re-auth. Server: Auth + CF. Destructive guarded.

## F10 Settings (N-37)
Business: session boundary, work types list, defaults, retention window, report flags. Owner only; validate enums/ranges; audit changes.

## Cross-field validation matrix
| Rule | Fields | Must be server-enforced? |
|---|---|---|
| session date unique | date+session | YES (transaction) |
| trip number unique within session | tripNumber | YES (CF) |
| driver/labour/vehicle active | refs | YES (rules/CF) |
| labour in roster before attendance | attendance.labourerId in trip roster | YES |
| corrections need reason on confirmed/completed | attendance | YES |
| role change preserves ≥1 owner | users | YES (CF) |

## Notes
- Client-side-only validation is insufficient — every cross-field/unique rule repeats on backend.
- Reference had no dedicated form framework gaps beyond missing filter; native forms unify via a shared form component + validation messages.
- Every form: state Submitting (disable), Success, Error(retry), Offline(queued+label), dirty handling (autosave or discard-confirm), reset/cancel.
- Verification PROPOSED; each form + validation is a Compose UI + repository/server test.
