# FINAL SCREEN STATE MATRIX — V1 (Phase 0.75)

For each V1 screen: the set of states it can enter and, per state, Trigger · UI · Allowed actions · Backend behaviour · Navigation · Recovery. Only relevant states listed. States keys: INITIAL, LOADING, SUCCESS, EMPTY, ERROR, OFFLINE, REFRESHING, SUBMITTING, UNAUTHORIZED, FORBIDDEN, SESSION_EXPIRED, PARTIAL_FAILURE, CONFLICT, RETRY.
Cross-cutting definitions in FINAL-UI-STATE-CONTRACT; only overrides noted here.

## Cross-cutting default behaviours (applied unless overridden)
- INITIAL → on-screen create → LOADING (skeleton) → SUCCESS/EMPTY.
- ERROR: typed message + Retry (idempotent); NEVER blank screen. Recovery = RETRY or navigate to allowed area.
- OFFLINE: banner + cached content or queued indicator; allowed read from cache; writes queue.
- UNAUTHORIZED: not signed in → redirect to N-03/N-08, preserve deep link/draft.
- FORBIDDEN: signed in, no permission → message + navigate to allowed area (owner shell).
- SESSION_EXPIRED: intercept → N-08; preserve outbox/drafts; after re-auth reconcile.

## Per screen
### N-01 Splash
INITIAL/LOADING (app-check+auth) → route home(N-10) if session : N-03. ERROR (app-check fail) → retry. No data states.

### N-03 Login
INITIAL (empty form) · SUBMITTING (disable + spinner) · SUCCESS (→ N-10 or deep link target) · ERROR (auth/network/rate/disabled → inline or error panel) · SESSION_EXPIRED (→ message to re-auth) · OFFLINE (if cached-session allowed: proceed to home; else "offline" notice, first sign-in blocked).
Retry allowed. Navigation: SUCCESS → N-10; FORBIDDEN(role not owner) → N-06/N-03 note.

### N-04/05 Password flows
INITIAL/SUBMITTING/SUCCESS(notice)/ERROR(no-enumeration, token expiry, network). Retry.

### N-06 Account disabled
INITIAL (notice). No data. Recovery: contact owner/support (out of app).

### N-07 Provisioning
INITIAL/SUBMITTING/SUCCESS(→N-10)/ERROR(network/conflict-account). Retry.

### N-08 Session expired
INITIAL(message)/SUBMITTING(re-auth)/SUCCESS(→ resume)/ERROR. Preserve drafts/outbox.

### N-10 Owner shell
SUCCESS (active nav). Loading handled by children. Session expiry/forbidden handled centrally.

### N-20 Dashboard
INITIAL/LOADING(skeleton)/SUCCESS(list)/EMPTY("No work today" + add)/ERROR(retry)/OFFLINE(cached + banner)/REFRESHING(pull)/SESSION_EXPIRED. Search: Searching sub-state.
PARTIAL_FAILURE not typical (read); CONFLICT n/a (read).

### N-22 Day/Session detail
INITIAL/LOADING/SUCCESS/EMPTY(no trips)/ERROR/OFFLINE(cached). Close action SUBMITTING → SUCCESS(closed)+audit / ERROR(conflict: already closed).
CONFLICT: two devices close same session → one wins; other shows "already closed [Reload]".

### N-23 Trip detail + attendance
INITIAL/LOADING/SUCCESS/EMPTY(no labour yet → add labour)/ERROR/OFFLINE(cached; writes queued)/SUBMITTING(save attendance)/**PARTIAL_FAILURE**(multi-row write partially failed → keep saved, flag failed [Retry])/**CONFLICT**(rev changed by another → merge/reload prompt)/SESSION_EXPIRED(draft preserved)/SUCCESS(after save: counts updated + snackbar).
Correction sub-state: reason required when editing completed/confirmed attendance.

### N-24 New/Edit Work Session
INITIAL (empty or prefilled for edit)/DRAFT-autosaved/SUBMITTING(save)/SUCCESS(pop+refresh)/ERROR(validation inline; conflict-uniqueness → offer open existing)/OFFLINE(queued+pending)/SESSION_EXPIRED(draft preserved). CANCEL → discard-confirm (if unsaved new) or keep(edit).

### N-25 Trip editor / next-trip
INITIAL(empty or copied-last)/DRAFT/SUBMITTING/SUCCESS(pop→N-23 refresh)/ERROR(validation/duplicate-number/offline)/OFFLINE(queued "pending sync"; no fake success)/SESSION_EXPIRED(draft preserved). 
CONFLICT: tripNumber race → server re-assigns on sync; UI shows corrected number.

### N-27 Reports/Analytics
INITIAL/LOADING(skeleton)/SUCCESS/EMPTY("record work to see reports")/ERROR(retry)/OFFLINE(cached)/REFRESHING. Export → SUBMITTING(progress)/SUCCESS(download)/ERROR(retry)/CANCEL.

### N-29/31/30 Catalogues
INITIAL/LOADING/SUCCESS/EMPTY(add)/ERROR/OFFLINE(cached)/SUBMITTING(save)/CONFLICT(rev on edit)/SESSION_EXPIRED. Remove → confirm → SUCCESS+undo → audit.

### N-36 Audit viewer
INITIAL/LOADING/SUCCESS/EMPTY(no events)/ERROR/OFFLINE(cached). Read-only. FORBIDDEN(non-owner) not reachable in V1.

### N-37 Settings
INITIAL(load current)/SUBMITTING(save)/SUCCESS/ERROR(validation)/CONFLICT(concurrent settings edit)/SESSION_EXPIRED. Save audited.

### N-38 Backup/restore
INITIAL/LOADING(current backups)/SUBMITTING(backup now progress; cancel)/SUCCESS(backup complete)/ERROR(storage/network/validation)/PARTIAL_FAILURE(restore verification mismatch)/CONFLICT(restore would overwrite newer data → confirm)/SESSION_EXPIRED. Restore: confirm → progress → verification → SUCCESS or ROLLBACK error state (pre-restore snapshot restored).

### N-39 Account & security
INITIAL/SUBMITTING(each action)/SUCCESS/ERROR(destructive requires re-auth)/SESSION_EXPIRED. Delete-my-data → multiple confirm + re-auth + audit.

### N-40 Profile
INITIAL(load)/SUBMITTING(save)/SUCCESS/ERROR(photo size/network)/OFFLINE(photo upload queued)/SESSION_EXPIRED. Role/email read-only.

### N-41 Notification centre (S4, owner)
INITIAL/LOADING/SUCCESS/EMPTY(no notifications)/ERROR/OFFLINE(cached; mark-read queued or on-online per decision)/SESSION_EXPIRED. Tap → deep link; NotFound target → friendly list.

### N-42 Help/About
SUCCESS(static). Empty/offline n/a.

## Verification
Every state above maps to a UI test (FINAL-TEST-CONTRACT) and a shared component (FINAL-UI-STATE-CONTRACT). No screen has an undefined critical state. V1-OPTIONAL screens (N-28,30,36,41,42) are still specified; driver/labourer screens excluded (D-1).
