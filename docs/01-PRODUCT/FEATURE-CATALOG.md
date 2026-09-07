# Feature Catalog

Purpose: Complete inventory of product features with current status. Status legend per `00-AUDIT-INDEX.md` (VERIFIED / PARTIAL / BROKEN / MISSING / PROPOSED).

Feature ID prefix: `F-`. Screen refs in `02-UX-UI/SCREENS.md`. Commit `2dd2fe4`.

| F-ID | Feature | Description | Evidence | Status |
|---|---|---|---|---|
| F-01 | Splash | Branded splash, auto-nav to dashboard | `splash_screen.dart` | VERIFIED WORKING |
| F-02 | Today dashboard | Auto date/session; summary card (morning/evening/total trips); quick counter | `dashboard_screen.dart` | VERIFIED WORKING |
| F-03 | Pull-to-refresh | RefreshIndicator reloads dashboard data | `dashboard_screen.dart` | VERIFIED WORKING |
| F-04 | Dashboard search | Live case-insensitive filter (driver/tractor/trip no/type/place) | `work_bloc.dart` | VERIFIED WORKING |
| F-05 | Dashboard filter | Filter event state exists | `FilterDashboardEvent`/`work_bloc.dart` | PARTIAL / UNREACHABLE in UI |
| F-06 | Add work+trip form | Work type, place, tractor chips, driver, labour list | `add_edit_work_screen.dart` | VERIFIED WORKING |
| F-07 | Next-trip quick add | Copy last trip context; confirm+save | `confirm_next_trip_screen.dart`, `work_bloc.dart` | VERIFIED WORKING |
| F-08 | Trip auto-numbering | Compute next across morning+evening | `CalculateNextTripNumberUseCase` | VERIFIED WORKING |
| F-09 | Edit trip/work | Preserve id/tripNumber/date/session on edit | router `extra`, `add_edit_work_screen.dart` | VERIFIED WORKING |
| F-10 | Labour master list | Names persist exactly as typed | `labour_model.dart`, repo | VERIFIED WORKING |
| F-11 | Labour add/edit/remove in trip | Dialog add, edit name, remove w/ undo | `trip_details_screen.dart` | VERIFIED WORKING |
| F-12 | Attendance toggle | per-trip present/absent persist | `trip_details_screen.dart` | VERIFIED WORKING |
| F-13 | Delete latest trip | minus button + confirm | dashboard | VERIFIED WORKING |
| F-14 | Delete specific trip | swipe-to-delete + confirm; cascade attendance | dashboard/details | VERIFIED WORKING |
| F-15 | History grouped | date→session expandable; drill/edit/delete | `history_screen.dart`, `history_bloc.dart` | VERIFIED WORKING |
| F-16 | Analytics | KPIs + sortable table; top driver | `analytics_screen.dart` | VERIFIED WORKING |
| F-17 | Backup | `.labourbackup` export (≤25 MB), SAF | `settings_screen.dart` | VERIFIED WORKING |
| F-18 | Restore | validate + snapshot rollback + count verify | `settings_screen.dart` | VERIFIED WORKING |
| F-19 | Draft autosave | on-change + lifecycle; restore snackbar | `add_edit_work_screen.dart` | VERIFIED WORKING |
| F-20 | Theme | Dark-only Material 3; theme mode tile inert | `app_theme.dart`, settings | PARTIAL (mode not switchable) |
| F-21 | About | Settings "About App" tile inert | `settings_screen.dart` | BROKEN/STUB (onTap `{}`) |
| F-22 | Details screen route | `/details` route + screen defined | `details_screen.dart`, router | PARTIAL — not reachable from nav; search & filter inert |
| F-23 | Auth/accounts | none | — | MISSING (by design today) |
| F-24 | Roles / RBAC | none | — | MISSING (by design today) → PROPOSED native |
| F-25 | Notifications | none | — | MISSING → PROPOSED native |
| F-26 | Cloud/backend/sync | none | — | MISSING → PROPOSED native |
| F-27 | Payments/reports service | none (light analytics only) | — | MISSING → PROPOSED native |
| F-28 | Accessibility features | minimal | see `02-UX-UI/ACCESSIBILITY-AUDIT.md` | MISSING/PARTIAL |
| F-29 | Localization | English hardcoded strings | see `ACCESSIBILITY`/globalization | MISSING |

## Feature count summary
- Total distinct feature rows: 29
- VERIFIED WORKING: 17
- PARTIAL/STUB/UNREACHABLE: F-05, F-20, F-21, F-22
- MISSING (intended future): F-23..F-29
- Feature parity → native mapping is in `08-NATIVE-ANDROID/FEATURE-PARITY-MATRIX.md`.
