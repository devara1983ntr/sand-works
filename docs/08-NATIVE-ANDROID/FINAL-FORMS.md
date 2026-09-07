# FINAL FORMS — V1 (Phase 0.75)

Every form field with type/required/validation/cross-field/format/keyboard/focus/error/server-validation/duplicate/unsaved/submit/cancel/reset/success/failure. Client validation is UX; **server/CF validation is authoritative**. Cross-reference FORM-VALIDATION-SPECIFICATION (Phase 0.5).

## Common submit contract (all forms)
- State Submitting disables action + spinner (double-submit safe, opId).
- Failure → typed error per FINAL-ERROR-CONTRACT; offline → queued+pending label (no fake success).
- Unsaved state: autosave draft for trip/session editors; discard-confirm for destructive/long forms on exit.
- Cancel: same as back semantics (autosave or discard-confirm). Reset: only where the form is a filter (clear) or explicit.

## FV-A Login (N-03)
- identifier (email): text, required, email format; keyboard email/IME done. Focus first. Server Auth validates. Error no-enumeration; rate-limited. Duplicate n/a. Submit → auth.
- password: password field, required, non-empty, policy length; IME done. Show/hide.
- Cross-field none.

## FV-B Provisioning (N-07)
- displayName (text, req non-empty); accept-terms (toggle req). Role/org set server-side (not in form). Phone optional. Success → home. Failure network/conflict(account exists).

## FV-C Password reset/recovery (N-04/05)
- email (req format); reset token; new password + confirm (match + policy). No enumeration; token expiry error.

## FV-D New/Edit Work Session (N-24)
- date (date picker, req, valid, format YYYY-MM-DD).
- session (segmented Morning/Evening, req, enum per D-5).
- workType (dropdown, req, default Sand, from options).
- place (optional text).
- status (open default; close not on this form).
- Cross-field: uniqueness (date+session) enforced server (CF) — duplicate → "already open, open/edit it".
- Draft autosave. Offline queue.

## FV-E Trip editor / next trip (N-25)
- tripNumber: read-only auto (server/CF), never editable.
- vehicle (dropdown from active catalogue), driver (dropdown req, active records), place (text opt), workType (default), notes (text opt multiline).
- labour roster: add via labourer picker (active), each with name; ≥1 labour to enable Save-with-attendance; remove with undo.
- Cross-field: driver/vehicle active; labour in org catalogue; number auto.
- Draft autosave (reference "next trip" prefill). Duplicate: server number prevents collision → on sync re-number shown.

## FV-F Attendance (N-23)
- per-labour presence toggle; add-labour (picker) ; remove labour (undo). 
- Correction path: if editing an already-saved/confirmed record → **reason required** (non-empty) + confirm; recorded to history.
- Cross-field: labour must be on the trip roster & in org; removed labour still shown historically.
- Server: CF writes + audit; partial-failure → keep saved/flag failed [Retry].

## FV-G Labourer/Driver/Vehicle catalogue (N-29/31/30)
- name (req, non-empty, preserve exact casing — no "Labour 1"), phone (opt, format), vehicle (driver: picker/text), active (toggle). Duplicate name → warn. Server validates + audit. Soft-delete confirm.

## FV-H Profile (N-40)
- displayName (req), phone (opt format), photo (image, type/size; photo picker). Role/email read-only. Self update only.

## FV-I Account & security (N-39)
- current password (req, re-auth for destructive), new password + confirm, delete-my-data (multiple confirm + re-auth), data export. Server/Auth/CF.

## FV-J Settings (N-37)
- workType default/options, hints, flags — validated enum/range; owner; audit; `rev` conflict.

## Cross-field / server-enforcement table
| Rule | Enforced server? |
|---|---|
| session uniqueness (date+session) | YES (CF txn) |
| trip number uniqueness/increment | YES (CF txn) |
| driver/vehicle/labour active & in org | YES (rules/CF) |
| labour in trip roster before attendance | YES (CF) |
| correction reason required | YES (CF) |
| role/status immutability | YES (rules/CF) |
| profile self-only fields | YES (rules) |

## Focus/IME/failure notes
- Autofocus first required field on editor screens; IME next→done; error under field with live-region announcement (a11y).
- Every field has a test: type/required/format/duplicate/cross-field/server-reject.
