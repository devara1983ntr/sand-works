# PRODUCT FREEZE — V1 (Phase 0.75)

Status: **FROZEN — RECOMMENDED SCOPE S-V1 (pending owner confirmation of scope + D-3/D-4).** This is the exact first-release scope. An implementation agent MUST NOT implement anything marked OUT OF SCOPE or DEFERRED.

Classification keys: MUST HAVE (V1 REQUIRED) · SHOULD HAVE (V1 OPTIONAL / important, may slip a patch) · COULD HAVE (future) · OUT OF SCOPE (explicitly excluded) · DEFERRED (known, postponed to V2+).

Contingency: If owner confirms scope S-V1, this freeze is binding. If owner changes D-1/D-2/D-6 (roles in V1), re-run this freeze before implementation.

---

## Product positioning (V1)
Native, offline-first Android rebuild of the verified single-operator labour/trip manager. One Owner operator records Work→Trips→Labour attendance, browses history, views analytics, and safely backs up/restores data. Online connectivity enables an Owner cloud account for durable backup & future multi-device; field data entry works fully offline and never shows false success.

## MUST HAVE (V1 REQUIRED)
Preserve verified core behaviour (F-01..F-19 owner path) with the Phase-0/0.5 fixes:
1. Owner authentication (single provisioned account) enabling cloud backup; app works offline after first sign-in. (N-01/03/07)
2. Session/day model Morning/Evening per verified boundary (D-5). (N-20/22)
3. Create/open Work Session (date+session, workType, place). (N-24)
4. Record Trips incl. sequential trip numbering across morning/evening & per date, copy-last-trip ("next trip") flow. (N-25)
5. Driver & tractor/vehicle selection from catalogue (driver = record, not account, in V1). (N-25/31/30)
6. Labourer catalogue management. (N-29)
7. Per-trip attendance with present/absent toggles; add/edit/remove labour with Undo; attendance history integrity (never silently overwrite; corrections carry reason + audit). (N-23)
8. Delete trip/work with confirmation + soft-delete + cascade + audit; numbering invariants preserved.
9. Grouped history (date→session) browsing with search; edit/tap-through. (N-27/history)
10. Dashboard: today's sessions/trips; search; add. (N-20)
11. Analytics: KPIs (works, trips, top driver) + sortable trip data table (FR-14 parity). (N-27)
12. Backup/restore: encrypted + signed `.labourbackup`; cloud backup (owner account) optional in parallel; validation, pre-restore snapshot, rollback, count verification. (N-38)
13. Draft autosave for new-trip forms (prevent loss). (N-24/25)
14. Owner profile & account settings: name/phone/photo; change password; sign out. (N-40/39)
15. Offline-first with explicit queued/pending-sync UI — **no fake success**. (all write screens)
16. Error handling with Retry everywhere; session-expiry handling that preserves drafts/outbox. (global)
17. Business settings: default workType/tractor hints, session boundary display; report flags. (N-37)
18. Security foundation: Firestore rules + App Check; server-authoritative values; audit of owner-privileged ops; encrypted local cache. (cross-cutting)
19. Theme: light/dark/dynamic (fix dark-only). (design system)
20. Accessibility, responsive (M3 adaptive), localization-ready string resources. (cross-cutting)

## SHOULD HAVE (V1 OPTIONAL)
- S1. CSV export of filtered/queried trip data. (N-27)
- S2. Tractor/vehicle catalogue manager screen (or merged into settings). (N-30)
- S3. Help/About screen with FAQ + version + support (replace inert reference tiles). (N-42)
- S4. Lightweight owner notification centre for system events (backup complete/restore/first new session reminder) with deep link. (N-41, owner-only)
- S5. Audit-log viewer for owner-privileged actions. (N-36, owner-only)
- S6. Search across full history (not just today). (N-27)
- S7. Localization beyond a single locale (structure only). 

## COULD HAVE (future)
- Custom/scheduled reports, wage/payment ledger, multi-period rollups (needs confirmed requirement).
- Pull-to-refresh offline revalidation UX polish.
- Widgets, wearable/second-screen.
- Data import from legacy `.labourbackup` (tied D-3).

## OUT OF SCOPE (explicitly excluded in V1)
- DRIVER or LABORER app logins / self-service journeys / own-data dashboards. (D-1)
- ADMIN delegation / multi-operator role management UI. (D-2)
- Driver-driven status workflow (assigned/accepted/in-progress) from the driver's device. (D-6)
- Cross-role notifications (assignment→driver, driver→owner status). 
- Payments, payroll, wage ledger (no confirmed requirement).
- Public/multi-tenant self-serve sign-up; user self-registration (owner is provisioned server-side).
- Web/desktop; biometric PII; ML/ad ML features.
- Automated migration from legacy Hive/data unless D-3 confirms real production data.

## DEFERRED (known, postponed)
- DE-1 Driver & labourer self-service + role homes (D-1).
- DE-2 ADMIN delegation (D-2).
- DE-3 Legacy Hive/`.labourbackup` import (D-3).
- DE-4 Rebrand/package decision resolution (D-4) — blocks public scaffold only, not private R&D.
- DE-5 Driver work-status state machine + assignment notifications (D-6).
- DE-6 Advanced reports / wage ledger (D-7 follow-on).
- DE-7 Final retention/privacy legal confirmation (D-8).

## Freeze governance
- Changes to MUST/OUT-OF-SCOPE require an owner-approved product change; do not silently expand.
- All screens/features below are defined in FINAL-FEATURE-CATALOG / FINAL-SCREEN-CATALOG / FINAL-WORK-STATE-MACHINE.
