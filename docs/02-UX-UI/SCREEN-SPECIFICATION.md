# Screen Specification (behavioural requirements per screen)

Purpose: Compact per-screen behavioural specification (current, VERIFIED) intended to be turned into acceptance tests for the native rebuild. Screen IDs from `SCREENS.md`. Commit `2dd2fe4`.

Format per screen: **S-ID** — must-haves (behavioural), plus state matrix.

## S-01 Splash
- Must show brand + app name + progress.
- Must auto-navigate to `/dashboard` after ~1.5 s.
- Must not block on network (offline-first).
States: single.

## S-02 Dashboard
Must:
- Auto-load current date + auto session on mount.
- On load → skeleton; on no data → empty state (CTA "Add First Trip"); on error → error text.
- Show summary (total trips, morning count, evening count), Current-Trip counter, Recent Trips.
- Support: pull-to-refresh, search toggle & live case-insensitive filtering, FAB "Add Work", Current-Trip plus (next trip), minus remove-latest (disabled when no trips), swipe-to-delete w/ confirm, tap → trip details.
- Return/reload after a save.
State matrix: Loading→Loaded/Empty/Error; no offline special-case.
**Gap:** error has no retry. **Gap:** filter not surfaced in UI.

## S-03 Details (orphaned)
- Reachable only by `/details`; not in nav.
- Header stats (date/session/trips) + trip breakdown list.
- Tap row → trip details; swipe edit/delete semantics confusing; search & filter inert.
**Recommendation:** either complete + wire into nav or remove (DEPRECATED in native).

## S-04 History
Must: load all works+trips; group date(desc)→session; first date expanded; per-trip: view/edit/delete; refresh; empty state; errors.
State matrix: Loading→Loaded/Empty/Error.

## S-05 Analytics
Must: load history; show Works/Trips/Top Driver KPIs; show trip data table; column-sort (date/session/trip/driver) with asc/desc toggle; horizontal scroll; empty state; errors.

## S-06 Settings
Must: Backup to `.labourbackup` (≤25 MB) via SAF; Restore from `.labourbackup` with validation, overwrite confirm, snapshot rollback, count verification, typed error dialogs; block UI while running.
Must show app version + "Fully Offline".
**Gap:** Theme Mode & About tiles inert — either implement or remove.

## S-07 Trip Details
Must: load trip details; show Trip/Work/Timeline cards; manage labours (add, edit name, toggle presence, remove w/ Undo); show total/present; empty state for no labours.
**Gaps:** edit action silently no-ops unless already in `TripDetailsLoaded`; labour name edit does not refresh the open list (BLoC comment); dead/duplicate switch branches.

## S-08 Add/Edit Work
Must: create (isNew) or edit (editingTrip+editingWork) a trip+work; guard editingWork present; date/session header; required work type & driver; tractor selector; ≥1 labour; autosave draft (create mode) on change/lifecycle pause; restore-draft prompt; on save pop + reload.
**Gaps:** throw exception guard; deterministic work id by date+session.

## S-09 Confirm Next Trip
Must: prefill from last trip (place/workType/attendance + next trip number); tractor/driver required; work-type dropdown; present/absent toggles; save & pop with reload; cancel.

## Acceptance-test seeds (native)
Each "Must" above becomes an Espresso/Compose UI test case. Empty/error/offline variants must be covered per `06-QUALITY/TESTING.md`.

## Verification status
VERIFIED against code. Native target tests PROPOSED in `08-NATIVE-ANDROID/`.
