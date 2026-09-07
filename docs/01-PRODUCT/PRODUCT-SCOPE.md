# Product Scope — Labour Party

## Purpose
Define exactly what is **in** and **out** of the current product, and what is **proposed** for the native rebuild, so no category is conflated.

Status: VERIFIED (from repository). Commit `2dd2fe4`. Audit date 2026-09-07.

## 1. In scope (current product, VERIFIED)

### Functional scope
- Record Work (date/session/work type/place).
- Record Trips under a Work (tractor, driver, auto trip number, place, work type, notes, status default `Completed`).
- Manage a labourer master list (name, optional phone).
- Record per-trip labour attendance (`present`/`absent`).
- Sequential trip numbering across Morning/Evening of the same date.
- Copy previous trip context into the next trip for speed.
- Today dashboard with session stats, search, swipe-to-delete, pull-to-refresh.
- Per-trip detail with labour management and undo-remove.
- Date/session grouped history with edit/delete/drill-in.
- Analytics: counts + top driver + sortable trip data table.
- Full local backup & restore (`.labourbackup`).
- Unsaved-draft autosave & restore.
- Offline-only Android app, Material 3 dark glassmorphism UI.

### Platform scope
- Android only. `minSdk 24`. Package/namespace `com.roshan.labourparty`.
- No iOS/macOS/web/Windows/Linux project scaffolding present in the repo (only `android/` + `assets/branding`).

## 2. Explicitly out of scope (current product, VERIFIED)
- Any authentication, user accounts, roles, permissions.
- Any network / cloud / backend / Firebase / sync / remote config.
- Payments, wages calculation engine, billing, invoicing.
- Reports as a first-class service (only lightweight analytics).
- Notifications (local or push), announcements.
- Multi-device / multi-operator collaboration.
- Permissions beyond SAF file pick (no runtime permission declared in main manifest).
- Web presence / SEO (native app). See note in `00-AUDIT-INDEX.md` (SEO: NOT APPLICABLE).

## 3. Proposed scope for native rebuild (PROPOSED)
These are **future, optional, and requirement-free until approved**:
- Server-authoritative Owner/Admin account; optional Driver and Labourer roles.
- Firebase Auth + Firestore + Storage + Cloud Functions + (App Check / Crashlytics / optional Remote Config).
- Server-enforced RBAC and audit logs.
- Role-scoped dashboards, job assignment, attendance sync, notifications.
- Cloud/offline-first backup strategy to complement local backup.
- Payments/records/reports modules only if product needs justify them.

## 4. Boundaries & trust model
- **Current:** trust = local device. All validation & data integrity is client-side.
- **Proposed:** trust = backend. Client-side role checks are explicitly **not** sufficient (see `04-SECURITY/SECURITY.md`).

## 5. Scope risks
- Risk that "proposed scope" is read as current functionality.
- Risk of silent data-loss if local backup is treated as cloud backup.
- Risk that offline-first requirement is dropped during native design.

## 6. Verification status
VERIFIED: scope statements traceable to `pubspec.yaml`, `README.md`, `PRD.md`, `AndroidManifest.xml`, `lib/**`. Open: OQ-1..OQ-3.
