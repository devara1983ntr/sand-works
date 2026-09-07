# FINAL BUSINESS RULES — V1 (Phase 0.75)

Rule fields: Rule ID · Rule · Actor · Precondition · Action · Validation · Result · Failure · Authorization · Database effect · Notification · Audit.
Enforcement: UI = client display/UX; RULE = Firestore security rules; CF = Cloud Function (server-authoritative). V1 single-owner scope (AUTHZ-OWNER).
Rules from the verified BR register are marked (BR-##) and preserved; new server rules are PROPOSED and mandated server-side.

## Work creation
- **R-01** (BR-1) A Work Session is unique per (orgId, date, session). Actor Owner. Pre: none. Action create. Validation uniqueness. Result open session. Failure conflict → offer open existing. Authz CF/RULE. DB workSessions. Audit AUD. 
- **R-02** date is a valid calendar date; session ∈ {Morning,Evening} per D-5 boundary. Validation client+server.
- **R-03** Only OWNER creates/opens WorkSessions in V1. Authz AUTHZ-OWNER. Failure Forbidden.

## Trip / numbering
- **R-10** (BR-2) Trip numbers are sequential per (date across Morning+Evening), first=1, next=max+1; new date resets. **Server/CF-authoritative** (not client). DB trips. Audit number allocation on create.
- **R-11** (BR-4) A trip belongs to exactly one session/date. Editing preserves id/tripNumber/createdAt/date/session.
- **R-12** A trip must have an active driver record (V1) and vehicle; may have ≥0 labour but Save of attendance requires ≥1 labour selected when recording present counts. Validation cross-field (driver/vehicle active).
- **R-13** Copy-last-trip ("next trip") copies driver/vehicle/place/labour; labour may be edited; number auto-increments.

## Attendance
- **R-20** (BR-3/BR-6/BR-8) Attendance stored per (trip, labourer); labour names persist as typed; attendance `isPresent` derived from status.
- **R-21** Only OWNER records attendance in V1. Corrections: reason required when changing completed/confirmed history; **never silently overwrite** (ATTENDANCE-INTEGRITY). Authz AUTHZ-OWNER/CF. Audit before/after+reason.
- **R-22** Adding/removing a labour from a trip must not delete the labourer master record; removal marks attendance removed (soft) preserving history.
- **R-23** A labourer must exist in the org catalogue to receive attendance.

## Delete / immutability
- **R-30** Deleting a trip soft-deletes trip + its attendance (cascade) preserving history + audit; numbering invariants preserved. Owner only, confirm, undo where reversible.
- **R-31** Deleting a WorkSession soft-deletes session + trips + attendance (cascade) via CF, audited.
- **R-32** Closing a session (R-40) makes it immutable-ish; further edits only within restricted correction window + reason + audit (D-8 default: corrections allowed but audited; no silent history rewrite).
- **R-33** Completed/confirmed attendance is not deletable; only correctable (reason+audit).

## Session lifecycle
- **R-40** Session status ∈ {open, closed}; open→closed by OWNER; close is audited + counter final. (Owner-driven; no driver states in V1.)

## Catalogues
- **R-50** Labourer/driver/vehicle records are org-scoped (AUTHZ-OWNER CRUD). Soft-delete keeps historical references; an inactive record is not assignable to new trips.
- **R-51** Labourer/driver names required non-empty; phone optional validated format; duplicate name → warn.

## User/account (V1 owner)
- **R-60** Only ONE OWNER active in V1 (provisioned). Role stored but only OWNER active; role/status changes only via CF (never client).
- **R-61** No client can change its own role, ownerId, createdAt, or status. Guard ≥1 active owner (trivially the single one in V1, still enforced server-side).
- **R-62** Account delete/my-data-delete via CF with re-auth + audit; org business records retained & anonymized; labourer/driver master not auto-deleted by owner-account deletion.
- **R-63** Password reset has no enumeration; rate limited.

## Reporting / export
- **R-70** Reports/analytics reflect org data the owner can read; CSV export carries no secrets and is produced from filtered owner data.
- **R-71** No wage aggregation unless a wage field exists (V1 does not store wages → no payroll).

## Backup/restore
- **R-80** Backup is encrypted + signed (fix plaintext risk); restore validates version/signature/checksum, snapshots pre-restore state, and rolls back on verification failure.
- **R-81** Restore may not silently overwrite newer cloud data without confirmation (CONFLICT handling).

## Retention / privacy
- **R-90** Owner owns data; no automatic deletion; soft-delete + audit; notification retention 30 d (if V1-OPTIONAL centre); PII minimised; no analytics PII (D-8 defaults).

## Failure summary
All R-rules: failure → typed error per FINAL-ERROR-CONTRACT; offline writes queue idempotently (no fake success); concurrency per CONCURRENCY-SPECIFICATION.

## Verification
Rules are VERIFIED (BR-## carried) or PROPOSED server rules; each maps to a rule test + unit test (FINAL-TEST-CONTRACT).
