# PRODUCT FREEZE — SAND WORKS V1

Scope governed by the locked directive. Implementation agent MUST NOT implement OUT OF SCOPE or DEFERRED. Status: FROZEN by directive.

## Positioning
Private, offline-tolerant, multi-role family/work-management system for Ramesh Sahu: tracks sand/material work, tractors, drivers, labourers, trips, money (rates/earnings), attendance, daily summaries, weekly/monthly leaderboards, alerts, notifications, and owner-only reports/export. Feels like a premium industrial operations system, not generic CRUD.

## MUST HAVE (V1)
**Identity & accounts**
1. Package `com.roshan.sandworks`; brand SAND WORKS; locked assets used for launcher + brand (from masters only).
2. Auth (OWNER/DRIVER/LABOURER sign-in) — real, backend-authoritative.
3. User approval flow: new driver/labourer registration → OWNER approval before privileged access.
4. Owner single admin; NO admin/delegate role.

**Role operations**
5. OWNER: users/drivers/labourers/tractors/work/trips/rates/money rules/approvals/attendance/reports/exports/alerts/notifications/settings/profiles/leaderboard/corrections/data mgmt.
6. DRIVER: sign in; view assigned/operational info; ADD TRIP; edit permitted trip info; select tractor; select labourers; view labour info; view own work/trip totals; share today's trip count via WhatsApp/share-sheet; own profile.
7. LABOURER (read-only operationally): view profile+picture, own working trips, total trips, earned & remaining money, working/absent days + dates, weekly & monthly leaderboards, notifications, daily earning summaries.

**Domain engine**
8. Trip system: date/time/tractor/driver/labourers/status/trip number(backend-authoritative)/rate snapshot/calculated total/metadata.
9. Tractor registry OWNER-managed; initial Sonalika + John Deere; add/edit/deactivate; every trip records tractor.
10. Rate system: default ₹200; owner-configurable; per-trip snapshot; historical trips immutable to future rate changes.
11. Money calc: totalTrips×rate; deterministic distribution (default equal among driver+eligible labourers); configurable rules; integer currency (no float errors); auditable inputs+outputs.
12. Daily summary 19:30 IST (configurable in 7–8 PM): closes day, computes accrued amounts, dedupe, notify eligible, audit record. Honest server-vs-client handling (§SCHEDULING-SPEC).
13. Labourer dashboard metrics (total/earned/remaining/working/absent/dates/weekly+monthly).
14. Leaderboards weekly + monthly, top-3, deterministic ties, real data only, no fake ranks.
15. Attendance/work-day tracking (working/absent date-based), owner-authorized corrections w/ reason + audit; no silent overwrite.
16. Temporary labour assignment w/ expiry (backend-enforced).
17. Owner alert/warning system (strongest compliant alert; honest Android limits).
18. Notifications A–F targeted by role/user (no private financial info broadcast).
19. Profiles (name/role/phone where permitted/picture/status/stats).
20. Owner-only export: PDF (human-readable) + CSV (raw). Multi-tractor/driver/labour/date breakdown.
21. Settings (rates, tractor registry, money rules, summary time, notifications, profiles).
22. Security foundation: backend-authoritative RBAC; every query rule-compliant; no client role/approval/money; audit server-authoritative; org-scoped.
23. Offline tolerance: drafts/queue/offline state/sync later; idempotency keys; explicit conflicts; no fake success/loss/duplicates.
24. UX: locked industrial design language + palette + Roboto + M3 (see UX-DESIGN-SYSTEM).
25. Accessibility + responsive (M3 adaptive) + quality bar (no placeholders/fake).

## SHOULD HAVE (V1 OPTIONAL)
- Profile photo upload via Firebase Storage — **only if Blaze** (else deferred/disabled; no fake Storage). §20.
- Cloud-scheduled daily closure via Cloud Functions — **only if Blaze** (else documented fallback). §12.
- Cloud backup (owner) — only if Blaze.
- Dark/light theme toggling polish; analytics where justified.

## COULD HAVE (future)
- Advanced distribution/payroll rules beyond those listed.
- Multi-organisation (currently single family org).
- Public distribution — NEVER (excluded).
- Extended reporting/trends.

## OUT OF SCOPE (explicitly excluded)
- Public release / Google Play. (§§1,3)
- ADMIN/delegated-admin role. (§3)
- Legacy Flutter data migration / compatibility. (§43)
- `com.roshan.labourparty` package identity. (§42)
- Any claim of forced full-volume override of silent/DND. (§17)
- Labelling the daily summary as "Payment completed" — accrued totals only. (§13)
- Labourer writes (add/edit/money/attendance/rates/approvals/export/settings). (§5)
- Fabricated/regenerated logo/icon/SVG/brand substitutes. (Asset rule)
- Fake Storage/Cloud-Functions on Spark plan. (§20/§12)
- Cross-role private financial broadcast. (§18)

## DEFERRED (known, postponed)
- Cloud Storage/Functions-dependent features if owner stays on Spark (implement fallback, defer the cloud-specific path).
- Cloud backup until Blaze decision.
- Any feature that the owner flags as later.

## Freeze governance
- MUST/OUT boundaries change only by explicit owner decision (directive + CHANGE-CONTROL in `09-IMPLEMENTATION`). Do not silently expand or drop.
