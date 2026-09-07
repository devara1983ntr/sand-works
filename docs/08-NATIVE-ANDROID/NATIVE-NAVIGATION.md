# Native Navigation

Status: PROPOSED (Phase 0). Determined from audited workflows, not forced names. Uses Navigation Compose; role-driven; adaptive on large screens.

## 1. Primary destinations
Current workflows (single-operator day entry + review + reports + settings). For the **OWNER/ADMIN** on a phone, recommended 4 primary destinations (≤5 guideline):
1. **Dashboard** — today's session(s), quick summary, quick-add.
2. **Work / Trips** — pick/date session, list trips, add/edit trip, attendance editor.
3. **Reports** — history/analytics KPIs, driver/labourer summaries.
4. **More / Menu** — crew (drivers/labourers), settings, account, backup/export, notifications badge.
(Destinations are working names pending D-1/D-6; crew/reports placement may fold.)

**DRIVER** (if self-service, D-1): My Trips → My Attendance → Profile/Settings (small).
**LABORER** (if self-service, D-1): My Attendance → Profile.
FCM notification → surfaces a top-bar badge + notification centre (self) reachable from More.

## 2. Structure & Back behaviour
```
graph
  AuthGraph (splash/onboarding → login → provisioning)
  MainGraph (role-based home + bottom destinations)   <- nested
  DetailGraph (trip editor, attendance, profile edit, reports detail)  <- pushed
```
- **Back:** predictable; from detail returns to originating list; from a tab returns/updates; Back at root exits (or to overview per Android guidance). Predictive-back supported.
- **Logout:** sign out → AuthGraph; confirm; offline outbox preserved with notice (not silently discarded).
- **Session expiry / Forbidden:** intercept → re-auth screen with clear message; never drop data.
- **Deep links:** navigation handles typed deep links (job/trip/attendance) but each target re-validates access via rules; extra-param passing (Flutter weakness BUG-17) replaced by stable IDs loaded via repository, never whole-object extras.

## 3. Large screens (tablets/foldables)
- Use `MaterialWindowSizeClass`/adaptive: NavigationRail on medium+; two-pane master–detail for Work lists→editor and Reports.
- Do not force phone bottom-bar layout onto tablets/foldables.
- Compose Material3 adaptive only where stable.

## 4. Screen reconciliation output
Each Flutter screen → native spec (see PRODUCT-RECONCILIATION + screen-by-screen in this doc's companion `IMPLEMENTATION-ROADMAP.md` Phase 1..). Provide loading/empty/error/offline/success states per screen at implementation.

## 5. Open decisions
D-1 (role home sets), D-6 (driver workflow). Choose the most-restrictive safe nav until resolved.

## 6. Verification
PROPOSED. ADR-013.
