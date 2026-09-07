# Screen Documentation (every screen)

Each screen includes the audited fields. Status: VERIFIED unless noted. Route map + back/drawer/bottom-nav analysis in `NAVIGATION-MAP.md`. Interaction details in `INTERACTION-SPECIFICATION.md`. Commit `2dd2fe4`.

> Accessibility, analytics and performance notes below reflect **current code**; proposed native treatments are in `08-NATIVE-ANDROID/`.

---

## S-01 SplashScreen — route `/splash`
- **Purpose:** branded launch; performs async init (Hive + DI already done in `main()`); then navigates to dashboard.
- **Role / auth / authz:** single app user; none.
- **Entry:** app cold start (initial route). **Exit:** `/dashboard` after 1500 ms timer (`Future.delayed` → `context.go('/dashboard')`).
- **Parent/child:** none (root). Not in bottom-nav shell.
- **App bar:** none. **Back button:** none (system back exits).
- **Content hierarchy:** centred column: brand image `assets/branding/app_icon_192.png` (120px), app name "Labour Party", accent `CircularProgressIndicator`.
- **Components:** `Image.asset`, texts, progress; animations `fade/scale/slideY` via `flutter_animate`.
- **States:** no data states (single static screen). On nav timer completion it always goes to dashboard.
- **Gestures/animations:** entrance animations only.
- **Accessibility:** image has no semantic label (`Image.asset` alt text not set) — TalkBack reads filename/path. **Finding.**
- **Analytics:** none.
- **Security/perf:** trivial.
- **Status:** VERIFIED WORKING.

## S-02 DashboardScreen — route `/dashboard` (bottom-nav tab 0)
- **Purpose:** home; today's Work + trips summary, quick-add, search, delete, list navigation.
- **Auth/authz:** none.
- **Entry:** `/splash` auto-nav; bottom nav. **Exit:** `/add-edit-work`, `/confirm-next-trip`, `/trip-details`, other tabs.
- **App bar:** custom: when not searching, brand image + "Labour Party" title; when searching, an inline `TextField` replaces the title. Actions: search/close toggle.
- **Back button:** none (top-level tab).
- **Bottom nav:** Dashboard/History/Analytics/Settings (via `MainLayout`).
- **Body states:** `WorkLoading/Initial → _buildSkeleton`; `WorkEmpty → EmptyStateWidget`; `WorkError →` centred message (no retry); `NavigateToConfirmNextTripState/WorkActionSuccess/TripDetailsLoaded → SizedBox` (routing/transient); `DashboardLoaded → _buildDashboard`.
- **Dashboard content hierarchy:** summary GlassCard (date • session, total trips chip, Morning/Evening stat items) → Current-Trip counter card (minus, number, plus) → "Recent Trips" header → trips list.
- **Recent trip list item:** `Dismissible` (swipe left to delete) wrapping `GestureDetector` → `GlassCard` row: trip-number avatar `#N`, tractor, "Driver: …", time.
- **Primary actions:** FAB "Add Work" (`/add-edit-work`, isNew) (shimmer animated), Current-trip plus (next trip), search.
- **Destructive actions:** minus (remove latest, disabled when no trips), swipe-to-delete with confirm dialog.
- **Search:** inline; on every change dispatches `SearchDashboardEvent`; toggling off clears + resets.
- **Pull-to-refresh:** `RefreshIndicator` reloads `LoadDashboardDataEvent`.
- **Loading/empty/error/offline:** offline = normal. Error no retry (finding).
- **Success:** after save, BLoC `WorkActionSuccess` triggers dashboard reload (this screen returns from push).
- **Gestures:** tap row → trip details; swipe left → delete; pull-to-refresh; FAB extended.
- **Empty CTA:** "Add First Trip" → `/add-edit-work`.
- **Accessibility:** Dismissible/Row semantics basic; no custom labels. **Finding** (contrast/semantics).
- **Analytics/security/perf:** none; in-memory + Hive reads; full list uses `ListView.builder` (shrinkWrap+non-scroll physics inside a scroll view).
- **Status:** VERIFIED WORKING.

## S-03 DetailsScreen — route `/details` (in shell but not nav-visible)
- **Purpose:** shows current day's trip breakdown ("Work Details"). Appears to be a near-duplicate/legacy sibling of Dashboard's trip list.
- **Reachability:** route defined in `app_router.dart` inside the `ShellRoute`; **no visible UI navigates here** (`grep '/details'` only in router). Only deep-link/manual.
- **App bar:** title "Work Details"; actions: search icon toggle and a filter icon; **search `onChanged: (value) {}` does nothing and filter `onPressed: () {}` does nothing** → inert controls (VERIFIED PARTIAL/BROKEN).
- **Body:** uses `WorkBloc` with same states as dashboard; header stats (Date/Session/Trips) GlassCard + "Trip Breakdown" list.
- **List item:** `Dismissible` horizontal; background(s) are **blue edit icons** but `confirmDismiss` maps `endToStart→delete confirm`, else→edit → **misleading swipe affordance** (finding). Tap → `/trip-details`.
- **Edit:** `_editTrip` → `/add-edit-work` with isNew:false, editingTrip/work.
- **Auth/roles:** none.
- **Status:** PARTIAL — orphaned route + inert search/filter + misleading delete swipe. Recommend either wire it properly into nav or remove in native (`DEPRECATED`).

## S-04 HistoryScreen — route `/history` (bottom-nav tab 1)
- **Purpose:** browse all recorded work grouped by date (desc) → session → trips.
- **Entry:** bottom nav. Loads via `LoadHistoryEvent` on `initState`. Refresh action reloads.
- **App bar:** "Work History"; actions: refresh icon.
- **Body states:** `HistoryLoading/Initial →` spinner; `HistoryError →` message; `HistoryEmpty → EmptyStateWidget` (note: empty CTA label, finding); `HistoryLoaded →` grouped list.
- **Grouped structure:** per-date `GlassCard` with `ExpansionTile` (first date initially expanded); inside, per-session header; per-trip `ListTile` (title `Trip #N - driver`, subtitle tractor, trailing edit + delete icons). Tap → `/trip-details`; edit → `/add-edit-work` (editingTrip/work); delete → confirm dialog then deletes + reloads history after 300 ms.
- **Auth/roles:** none.
- **Empty:** EmptyStateWidget; **CTA blank label** (shared component always draws button).
- **Status:** VERIFIED WORKING (with minor CTA-label issue).

## S-05 AnalyticsScreen — route `/analytics` (bottom-nav tab 2)
- **Purpose:** compute KPIs & show sortable trip data table from history data.
- **Entry:** bottom nav → `LoadHistoryEvent`.
- **App bar:** "Analytics". Body states mirror History.
- **Content:** KPI row (Works / Trips / Top Driver) via three GlassCards; "Data Table" GlassCard with horizontally scrollable `DataTable` (Date, Session, Trip, Driver, Tractor). Column header tap sorts (date/session/trip/driver; ascending toggle; tractor not sortable).
- **Aggregation:** counts works/trips; driver & tractor ranking; builds flat list of trips (no labour loaded — memory note).
- **Empty:** EmptyStateWidget with blank CTA label (finding).
- **Auth/roles:** none.
- **Status:** VERIFIED WORKING (minor empty-state label issue).

## S-06 SettingsScreen — route `/settings` (bottom-nav tab 3)
- **Purpose:** local backup/restore and (inert) theme/about.
- **Entry:** bottom nav.
- **App bar:** "Settings".
- **Body (loading):** full-screen spinner while backup/restore runs (`_isLoading`).
- **Sections:** GlassCard: "Backup Database" tile → `_backupDatabase`; "Restore Database" tile → `_restoreDatabase`. GlassCard: "Theme Mode" (shows "Dark", `onTap: () {}` inert); "About App" (`onTap: () {}` inert). Footer text: "Labour Party v1.0.0 Fully Offline".
- **Backup flow:** SAF save-file; JSON of all 4 boxes; success snackbar.
- **Restore flow:** SAF pick; extension check; off-thread parse/validate (25 MB, structure); summary+overwrite dialog; snapshot→clear→apply→verify counts→snackbar; rollback on failure; specific error dialogs (Too Large / Unsupported / Invalid / generic).
- **Auth/roles:** none.
- **Destructive:** restore overwrites current data (requires confirm dialog).
- **Accessibility:** file flows depend on platform pickers.
- **Status:** VERIFIED WORKING for backup/restore; Theme/About tiles PARTIAL/inert.

## S-07 TripDetailsScreen — route `/trip-details` (pushed, extra: `Trip`)
- **Purpose:** per-trip detail: info, work info, timeline, labour/attendance management.
- **Entry:** tap trip row on dashboard/details/history. Loads `LoadTripDetailsEvent(trip.id)` on init.
- **App bar:** `Trip #N`; actions: edit (pencil) → `/add-edit-work` (isNew:false + editingTrip/work/labours) **only when state is `TripDetailsLoaded`** (otherwise silently does nothing — finding); add-labour (person_add) → dialog.
- **Body states:** spinner; then `TripDetailsLoaded` shows:
  - Trip Info card (Tractor, Driver, Time, Status)
  - Work Info card (Work Date, Session, Place, Work Type, Notes)
  - Timeline card (Created, Last Modified, Duration mins)
  - Labour Details: header + "Total/Present"; per-labour row (left accent border green=present/red=absent, name, edit icon, presence Switch, remove icon w/ Undo snackbar). If none: "No labours assigned to this trip".
  - Add/edit labours via dialogs.
- **Presence toggle:** `UpdateTripLabourEvent`. **Remove:** `DeleteTripLabourEvent` + Undo → re-save.
- **Auth/roles:** none.
- **Destructive:** labour remove (with undo); no trip-delete from here.
- **Perf:** loads all labours list each time (`getLabours`) to resolve names (potential O(n×m) name lookups — see `05-PERFORMANCE/`).
- **Status:** VERIFIED WORKING (some dead/duplicate switch cases; name-edit refresh gap).

## S-08 AddEditWorkScreen / "Add Trip" — route `/add-edit-work` (pushed, extra map)
- **Purpose:** create a new Work+Trip, or edit an existing trip/work.
- **Entry:** dashboard FAB/empty CTA (isNew true); details edit; trip-details edit; history edit (isNew false + editingTrip/work/labours).
- **App bar:** "Add Trip"; custom **back arrow** `leading` that saves draft then `context.pop()`.
- **Guard (VERIFIED):** if `editingTrip != null && editingWork == null` → throws Exception at init (defensive).
- **Body:** `Form` in `SingleChildScrollView`. Sections:
  - Header GlassCard (Date, Session).
  - Work Details: Work Type (required), Place (optional).
  - Trip Details: tractor selector chips (Sonalika / JohnDeere) writing controller; Driver Name (required).
  - Labour List: add-labour icon (dialog) + rows (name, presence Switch, remove). Empty → message "No labours added. Please add at least one."
  - Save (PremiumButton "Save Trip"). Save guard: form valid AND ≥1 labour.
- **Draft autosave:** every controller change (and on lifecycle pause/inactive) writes a `DraftModel` into `draft_box` (only when creating, not editing); on open, if a draft exists shows "unsaved draft" snackbar with Restore.
- **Save action:** `_clearDraft()`; builds deterministic Work id; tripNumber 0 (auto) or preserved on edit; dispatches `SaveFullWorkTripEvent`; on `WorkActionSuccess` snackbar "Trip Saved Successfully!" and pops.
- **Auth/roles:** none.
- **Error:** invalid form inline; no-labour snackbar.
- **Status:** VERIFIED WORKING.

## S-09 ConfirmNextTripScreen — route `/confirm-next-trip` (pushed, extra map incl. Work/nextTripNumber/previousLabours/place/workType)
- **Purpose:** review & save the "next trip" (copy of previous trip context + attendance).
- **Entry:** dashboard current-trip plus → BLoC `NavigateToConfirmNextTripState` → dashboard listener builds extras → push.
- **App bar:** "Confirm Next Trip". (System back = default pop; no unsaved-draft handling here.)
- **Body:** Form: Next Trip Overview card (work date/session, next trip #); Trip Details (Tractor required, Driver required, Work Type dropdown Sand (Bali)/Soil/Stone/Custom, Place, Notes); Labour & Attendance card (SwitchListTile per labour Present/Absent); Save ("Save as Next Trip") + Cancel.
- **Save:** builds new Trip (tripNumber = passed next) + TripLabour rows; dispatches `SaveNextTripEvent`; pops; snackbar; reload dashboard via listener on `WorkActionSuccess`.
- **Auth/roles:** none.
- **Status:** VERIFIED WORKING.

---

## Cross-cutting notes
- **App bars:** all screens except splash have an AppBar; transparent bg, centred title (theme).
- **Back arrow:** default on pushed routes (S-07/08/09); custom on S-08. Top-level tabs (S-02/04/05/06) have no back.
- **Drawer/hamburger:** none anywhere (VERIFIED).
- **Bottom nav:** Dashboard/History/Analytics/Settings only (VERIFIED) — matches 4-destination guideline.
- **Inert controls finding:** Settings Theme/About; Details search/filter; AddQuickTripEvent handler empty.
