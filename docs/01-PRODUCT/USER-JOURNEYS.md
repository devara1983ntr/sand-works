# User Journeys

Purpose: End-to-end journeys grounded in the current implementation (VERIFIED) with proposed deltas for native (PROPOSED). Each step maps to screens/routes documented in `02-UX-UI/SCREENS.md` and `02-UX-UI/UX-FLOWS.md`.

Status: VERIFIED (code-traced) unless labelled PROPOSED. Commit `2dd2fe4`.

## Journey 1 — Start a day (VERIFIED)
1. Launch → `/splash` (~1.5 s) → `/dashboard`.
2. Dashboard auto-loads today + auto session.
3. If empty → empty-state; user taps **Add First Trip** → `/add-edit-work` (new).
4. Fill work type/driver/labour(s) → Save → `WorkActionSuccess` → pop → dashboard reloaded.

## Journey 2 — Add the next trip quickly (VERIFIED)
1. Dashboard, Current-Trip card → **+** → BLoC copies last trip place/workType + labours.
2. `/confirm-next-trip` prefilled; user sets tractor/driver, toggles attendance, Save.
3. New trip number = max(+1). Dashboard reloads.

## Journey 3 — Record attendance on a trip (VERIFIED)
1. Tap a trip row → `/trip-details`.
2. Toggle each labour Present/Absent (or Add/Edit/Remove labour).
3. Reload shows updated present count.

## Journey 4 — Search / find a trip today (VERIFIED — search only)
1. Dashboard → search icon → type driver/tractor/#/type/place.
2. Live filtering via `SearchDashboardEvent`.
3. No filter UI in current app (filtering reachable only via state, not UI). PROPOSED: add UI filters for native.

## Journey 5 — Delete a mistake trip (VERIFIED)
1. Swipe trip left OR use minus on current trip OR delete from history.
2. Confirmation dialog → delete → cascade-removes attendances → refresh.

## Journey 6 — Review history (VERIFIED)
1. Bottom nav **History** → grouped by date (desc) → session → trips.
2. Expand a date; tap a trip → details; edit/delete icons.

## Journey 7 — Review analytics (VERIFIED)
1. Bottom nav **Analytics** → KPIs (works/trips/top driver) + sortable data table.

## Journey 8 — Protect data: backup & restore (VERIFIED)
1. Settings → Backup → SAF save `.labourbackup`.
2. Settings → Restore → pick file → validate → confirm overwrite → apply w/ snapshot rollback → success.

## Journey 9 — Recover unsaved form (VERIFIED)
1. Start adding a trip, leave/app-pause → draft autosaved.
2. Re-open add → snackbar "You have an unsaved draft." → Restore.

## Journey 10 (PROPOSED) — Owner logs in & manages crew
1. Firebase Auth sign-in → backend role resolution → owner dashboard.
2. Manage drivers/labourers, assign jobs, view reports; all privileged ops server-enforced.

## Journey 11 (PROPOSED) — Driver receives assigned work
1. Push notification (FCM) → deep link to assigned job → update status/completion (authorized server-side).

## Journey 12 (PROPOSED) — Labourer checks attendance
1. Sign-in (if labourer role exists) → view their attendance/pay for the period; server-filtered to own records.

## Journey map (current)
`/splash → /dashboard ⇄ /add-edit-work` , `/dashboard ⇄ /confirm-next-trip` , `/dashboard → /trip-details`, bottom-nav shell: `/dashboard`, `/history`, `/analytics`, `/settings`; `/details` route defined but not reachable from visible nav (see `NAVIGATION-MAP.md`).

## Verification status
VERIFIED J1–J9. PROPOSED J10–J12 (require backend not present today).
