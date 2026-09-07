# Native Android Rebuild — Product Requirements (draft)

Status: PROPOSED (draft for the future native product). No native app exists yet. It supersedes/augments the current offline single-user product per the migration brief. Commit audited `2dd2fe4`.

## 1. Positioning
Rebuild "Labour Party" as a native Android app (Kotlin + Jetpack Compose + Material 3) that preserves the **validated core business behaviour** of the current offline product while adding secure multi-role operation and an optional backend. Offline-first remains a priority (field usage).

## 2. Retain from current product (VERIFIED behaviour to carry over)
- Core domain: Work/Session, Trips, Labour, per-trip attendance.
- Sequential trip numbering across morning/evening and per date.
- Data integrity on edit/delete/restore (no orphans; cascade delete; soft-mark removed attendance).
- Quick "next trip" copy from the previous trip.
- Grouped history + analytics; search.
- Local backup/restore as a portable export.
- Draft autosave to prevent data loss.
- Field-oriented, fast data entry (chips for tractor, quick attendance toggles).

## 3. Change / improve (PROPOSED)
- Add secure authentication and explicit roles (Owner/Admin, Driver, Labourer where justified) with **server-authoritative** authorization.
- Add optional Firebase backend (Auth, Firestore, Storage, Cloud Functions, FCM) while retaining offline-first local behaviour.
- Fix known defects: signed/encrypted backups, retry on errors, accessibility, contrast, localization-ready strings, adaptive/tablet layouts, proper theme/light mode, no orphaned routes or inert controls.
- Reports & payment ledger only if product need is confirmed.

## 4. Explicit non-objectives for v1 native (PROPOSED)
Do not over-engineer: no web/desktop unless required; no analytics/advertising; no biometric PII; keep dependencies justified.

## 5. Roles
See `04-SECURITY/RBAC.md`. Owner "Ramesh Sahu" identity must be represented server-side at provisioning, **never hardcoded**. Resolve branding/package identity (OQ-2).

## 6. Acceptance themes
- Role-gated journeys work end-to-end (owner assigns → driver completes → owner reports).
- Offline entries queue and reconcile without data loss.
- All critical-journey tests pass; security rules tested; release gate (PRE-RELEASE.md) passes.

## 7. Success metrics (targets — not measured)
Crash-free ≥99.5%; cold start ≤2 s; data entry fast; zero silent data-loss; successful offline→online reconcile 100% on tested paths.

## 8. Open questions
OQ-1..OQ-8 (see product/security docs). Everything marked PROPOSED pending confirmation.
