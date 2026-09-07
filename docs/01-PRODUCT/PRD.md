# PRD — Labour Party (Flutter reference implementation audit)

## 0. Document metadata

| Field | Value |
|---|---|
| Purpose | Authoritative product requirements document reconstructed from the existing Flutter repository |
| Scope | Existing product behaviour only; future native Android requirements are separately labelled `PROPOSED` |
| Status | **VERIFIED (from repository & evidence)** where stated; gaps marked `UNVERIFIED` / `PROPOSED` |
| Evidence/source | `README.md`, `PRD.md` (root), `lib/**`, `docs/PRODUCT_OVERVIEW.md`, `pubspec.yaml` |
| Repository | `https://github.com/Manash07Bhoi/LABOUR-PARTY-2.git` |
| Commit audited | `2dd2fe4ed85e9f0e2a420e3a383fed1e75b8a21b` |
| Audit date | 2026-09-07 |
| Classification guide | `VERIFIED` = observed in code; `INFERRED` = strongly supported but not directly observed; `PROPOSED` = future recommendation; `UNVERIFIED` = could not be confirmed |

> **IMPORTANT SCOPE NOTE (VERIFIED).** The existing product is an **offline-only, single-user, local-first** Android application. It has **no authentication, no user accounts, no roles, no backend, no network, and no cloud sync.** The owner/driver/laborer role model and Firebase backend requested in the migration brief are **future objectives, not current functionality.** They are documented as `PROPOSED` and are detailed in `08-NATIVE-ANDROID/` and `04-SECURITY/RBAC.md`. Do not confuse these categories.

---

## 1. Executive summary

**Labour Party** is an offline-first labour/trip management application for Android. It lets an operator (the app owner) record a day's work broken into **Work → Trips → Labour** records, track tractors and drivers per trip, mark labourer attendance per trip, browse history by date/session, view basic analytics, and export/import the local dataset as a `.labourbackup` file.

The product is explicitly scoped (per the root `PRD.md`) to: **No login, No internet, No cloud sync, No multi-user, No external backend.** All data lives inside Hive NoSQL boxes in the Android application sandbox.

## 2. Product vision

`INFERRED` (from code, README, and existing docs): A fast, reliable, 100 % offline tool for a single operator to run and record a labour/transport day (sand, soil, stone haulage with tractor trips) without depending on connectivity, cloud services, or per-user accounts.

## 3. Problem statement

`INFERRED`: Manual record keeping of daily tractor trips and daily-wage labour attendance is error-prone and hard to reconstruct. The app digitises the ledger on-device with strong data-integrity and trip-numbering rules and a backup file so records are not lost when the device is changed.

## 4. Objectives

| ID | Objective | Category |
|---|---|---|
| OBJ-1 | Record a `Work` (date + session + work type + place) | VERIFIED |
| OBJ-2 | Record many `Trip` records under a work (tractor, driver, trip number, time) | VERIFIED |
| OBJ-3 | Track labour participation & attendance per trip (`isPresent`) | VERIFIED |
| OBJ-4 | Maintain correct sequential trip numbering across morning/evening sessions and across dates | VERIFIED |
| OBJ-5 | Preserve data integrity on delete/edit/restore (no orphaned records) | VERIFIED (intent; partial gaps) |
| OBJ-6 | Provide history browsing grouped by date/session | VERIFIED |
| OBJ-7 | Provide lightweight analytics (counts, driver ranking, tractor usage table) | VERIFIED |
| OBJ-8 | Export/import local data via `.labourbackup` | VERIFIED |
| OBJ-9 | Autosave in-progress form as a draft to prevent data loss | VERIFIED |
| OBJ-10 | Operate fully offline | VERIFIED |

## 5. Non-objectives (VERIFIED — current product)

- No login / registration / password recovery.
- No multi-user or role-based access control.
- No cloud backend / Firebase / remote APIs / sync.
- No online analytics or crash reporting (external).
- No payments, reports-as-service, notifications, announcements.
- No web presence / SEO.

These are **current** non-objectives. The migration brief proposes changing many of them for the native product (see `PROPOSED` sections).

## 6. Target users

| Persona | Current (VERIFIED) | Future (PROPOSED) |
|---|---|---|
| App owner / operator (a labour-contractor / transport operator) | Only user of the app | Primary admin (brief names owner as "Ramesh Sahu" — NOT present in code) |
| Driver | Not a separate app user (driver is a data field on a Trip) | Potential role |
| Labourer / worker | Not an app user (labourer is an entity + attendance toggle) | Potential role |

> The owner identity **"Ramesh Sahu" does not appear anywhere in the repository source** (grep `ramesh|sahu` returned no hits). The Android package/namespace id is `com.roshan.labourparty`. This is a documented discrepancy — see `03-ENGINEERING/AGENT.md` and `08-NATIVE-ANDROID/`.

## 7. Roles

| Role | Current implementation | Access model |
|---|---|---|
| App user (implicit owner) | Unauthenticated; all features available | No auth gate (VERIFIED) |
| Owner / Admin | N/A — no auth, no role data | PROPOSED (backend-authoritative) |
| Driver | A `driverName` string on Trip; no account | PROPOSED |
| Labourer | A `Labour` entity + per-trip attendance; no account | PROPOSED |

See `04-SECURITY/RBAC.md` for the RBAC matrix.

## 8. Core domain concepts (VERIFIED)

- **Work** — top-level record grouping a session of trips. Fields: `id, date, session, workType, place, createdAt, updatedAt` (`lib/features/work/domain/entities/work.dart`).
- **Trip** — one transport cycle. Fields: `id, workId, tripNumber, tractor, driverName, createdAt, place, workType, notes, updatedAt, status` (`.../entities/trip.dart`).
- **Labour** — a worker record. Fields: `id, name, phoneOptional?, createdAt` (`.../entities/labour.dart`).
- **TripLabour** — attendance join between Trip and Labour. Fields: `id, tripId, labourId, isPresent` (`.../entities/trip_labour.dart`).
- **Draft** — transient autosave of an in-progress new-trip form (`.../data/models/draft_model.dart`).

## 9. Use cases (primary)

| UC | Description | Status |
|---|---|---|
| UC-1 | Open app → splash → dashboard for today's date & auto-detected session | VERIFIED |
| UC-2 | Add a work+trip via "Add Work"/"Add First Trip" form | VERIFIED |
| UC-3 | Add the "next trip" by copying the last/latest trip & labours, editing, confirming | VERIFIED |
| UC-4 | View today's trip list, search by driver/tractor/trip no./work type/place | VERIFIED (search) |
| UC-5 | Open trip details; add/edit/remove labour; toggle attendance | VERIFIED |
| UC-6 | Delete the latest trip or a specific trip (with confirm) | VERIFIED |
| UC-7 | Browse history grouped by date→session | VERIFIED |
| UC-8 | View analytics KPIs + sortable trip data table | VERIFIED |
| UC-9 | Backup database to `.labourbackup`; restore with rollback | VERIFIED |
| UC-10 | Resume an unsaved draft | VERIFIED |

## 10. Functional requirements (FR) — current

> Requirement IDs are assigned by this audit for traceability. Status legend per `00-AUDIT-INDEX.md`.

| FR-ID | Requirement | Implementation evidence | Status |
|---|---|---|---|
| FR-01 | App launches to `/splash`, auto-navigates to `/dashboard` after ~1.5 s | `splash_screen.dart` `Future.delayed(...context.go('/dashboard'))` | VERIFIED |
| FR-02 | Dashboard auto-loads current date and derived session | `dashboard_screen.dart` `initState`; `DateTimeUtils.getCurrentSession()` | VERIFIED |
| FR-03 | Session derivation: hour 4–11 → Morning else Evening | `core/utils/date_time_utils.dart` | VERIFIED |
| FR-04 | Add new Work+Trip with validation (work type, driver required; ≥1 labour) | `add_edit_work_screen.dart` `_saveWork` | VERIFIED |
| FR-05 | Preserve date/session isolation when editing (throw if `editingTrip` without `editingWork`) | `add_edit_work_screen.dart` initState | VERIFIED |
| FR-06 | Compute next trip number across morning+evening works for a date | `work_usecases.dart` `CalculateNextTripNumberUseCase` | VERIFIED |
| FR-07 | Copy last trip's labours & place into a new "next trip" | `work_bloc.dart` `_onNavigateToConfirmNextTrip` | VERIFIED |
| FR-08 | Save a new next trip and its labour attendances atomically-ish | `work_bloc.dart` `_onSaveNextTrip`; `_onSaveFullWorkTrip` | VERIFIED (see integrity notes) |
| FR-09 | Search dashboard trips (driver, tractor, trip number, work type, place; case-insensitive) | `work_bloc.dart` `_onSearchDashboard` + `SearchDashboardEvent` | VERIFIED |
| FR-10 | Filter dashboard | `FilterDashboardEvent` exists in state layer | **PARTIAL / UNREACHABLE** — no UI dispatches filter; filter predicate not implemented (always true) |
| FR-11 | Delete latest trip / specific trip with confirmation | `_onRemoveLatestTrip`, `_onDeleteSpecificTrip`; dialogs | VERIFIED |
| FR-12 | Trip detail: view info, add labour, edit labour name, toggle presence, remove labour (with Undo) | `trip_details_screen.dart` | VERIFIED |
| FR-13 | History grouped by date & session, expandable; edit/delete/tap-through | `history_screen.dart`; `history_bloc.dart` | VERIFIED |
| FR-14 | Analytics KPIs (works, trips, top driver) + sortable trip data table | `analytics_screen.dart` | VERIFIED |
| FR-15 | Backup to `.labourbackup` (versioned JSON, ≤25 MB), SAF file save | `settings_screen.dart` `_backupDatabase` | VERIFIED |
| FR-16 | Restore from `.labourbackup` with validation, pre-restore snapshot, rollback, count verification | `settings_screen.dart` `_restoreDatabase` | VERIFIED |
| FR-17 | Autosave draft on field change / lifecycle pause; offer restore snackbar | `add_edit_work_screen.dart` | VERIFIED |

## 11. Non-functional requirements (NFR) — current

| NFR-ID | Requirement | Status |
|---|---|---|
| NFR-01 | 100 % offline operation; no internet permission in main manifest | VERIFIED |
| NFR-02 | Dark-only Material 3 UI with glassmorphism & animation | VERIFIED |
| NFR-03 | Data persists across restarts via Hive | VERIFIED |
| NFR-04 | Cold start shows splash + skeleton loading | VERIFIED |
| NFR-05 | Bulk `TripLabour` writes use `putAll`/prefix-key scans for performance | VERIFIED (see `05-PERFORMANCE/`) |
| NFR-06 | No secrets/keys in source (client has no secrets) | PARTIAL — see `keystore.jks.bak` finding |
| NFR-07 | Accessibility, localization, RTL, tablet layout | **MISSING / minimal** (see audits) |

## 12. Business rules (VERIFIED, summary)

See `01-PRODUCT/BUSINESS-RULES.md` for the full register. Headline rules:
- BR-1 Work is uniquely scoped by `(date, session)`; dashboard queries today's date/session.
- BR-2 Trip number is derived: first=1, next=max(previous)+1 across morning & evening, edit preserves, delete preserves history, new date/session resets.
- BR-3 Labour names persist exactly as typed ("never `Labour 1`").
- BR-4 A trip must belong to one date/session/work.
- BR-5 Deleting a trip must cascade-delete its `TripLabour` attendance rows.
- BR-6 Attendance `isPresent` must be stored per trip per labour.

## 13. Dependencies, assumptions, constraints

- **Dependencies**: `flutter_bloc`, `go_router`, `hive`/`hive_flutter`, `get_it`, `equatable`, `dartz`, `uuid`, `intl`, `flutter_screenutil`, `file_picker`, `google_fonts`, `flutter_animate`, `glassmorphism_ui`, `path_provider`, `cupertino_icons`. Dev: `flutter_lints`, `build_runner`, `hive_generator`, `mocktail`. (See `03-ENGINEERING/THIRD-PARTY-DEPENDENCIES.md`.)
- **Assumptions**: single device per install; operator trusts their own device; no multi-tenancy needed.
- **Constraints**: `minSdk 24`, Android only, offline, no backend. Package id `com.roshan.labourparty`.

## 14. Success metrics (PROPOSED for native; no telemetry currently)

Because the product is offline and has no analytics, **no runtime success metrics are measurable** (VERIFIED absence). Metrics below are PROPOSED targets for the native rebuild:
- Data entry time per trip < 30 s; crash-free sessions ≥ 99.5 %; zero silent data-loss incidents; successful backup/restore round-trip 100 %.

## 15. Acceptance criteria (current, inferred from PRD & tests)

- A new day with no records shows the empty dashboard.
- Adding the first trip yields Trip #1; appending across sessions increments correctly (covered by `test/unit/usecases/trip_numbering_test.dart`).
- Editing a past trip preserves its `id`, `tripNumber`, `createdAt`, `date`, `session`.
- Restoring an invalid/large backup is rejected without destroying current data.
- Deleting a trip removes it from dashboard and history consistently.

## 16. Risks

| Risk | Detail | Status |
|---|---|---|
| Committed keystore/private-key backup file | `android/app/keystore.jks.bak` tracked | **CRITICAL** — see `04-SECURITY/` |
| No authentication/RBAC | Any device holder with app can read/write all labour & phone data | HIGH (as product grows) |
| Local-only = single point of failure | No cloud backup | MEDIUM (by design today) |
| Backup file plaintext PII | Labour names + optional phone numbers exported unencrypted | MEDIUM |
| Duplicate top-level `LabourFormModel` declarations & duplicated switch cases | Code-maintainability/dead-code risk | MEDIUM/LOW |

## 17. Future opportunities (PROPOSED)

- Backend-backed owner/driver/laborer roles with server-enforced RBAC.
- Optional Firebase sync/backup while retaining offline-first behaviour.
- Reports (daily wage tally, material totals), payments ledger.
- Notifications & announcements.
- Multi-device / company account structure.

## 18. Open questions

- OQ-1 Who operates the app in production and is a single-operator assumption correct? `UNVERIFIED`
- OQ-2 Intended owner identity (the brief's "Ramesh Sahu" vs package id "roshan"). `UNVERIFIED`
- OQ-3 Is any cloud/backend planned for the native product, or must it stay offline-only? `UNVERIFIED`
