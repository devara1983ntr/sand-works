# Phase 5 — OWNER Surfaces — Task Contracts

Phase objective: complete OWNER app surfaces per `10-SANDWORKS/SCREEN-CATALOG.md`. Each screen task carries the mandatory sub-checklist (state contract / ViewModel / repo-data source / UI / all states / a11y / responsive / tests). Prereq phases 1-4 (gates). Execution gated by Gate-0/env.

Sub-checklist (applies to every screen task; none may be skipped): (1) sealed UiState per screen; (2) UDF ViewModel with action→reaction (no fake success); (3) repository/data-source consumption (local-first, CF privileged); (4) UI components (real behaviour, no dead/inert/placeholder); (5) states Loading/Empty/Error/Offline/Submitting/Conflict(where)/Forbidden/Unauthorized/SessionExpired truthful; (6) accessibility gate PASS; (7) responsive/adaptive; (8) tests (happy/empty/loading/error/offline/forbidden + interaction). See `10-SANDWORKS/SCREEN-STATE-CONTRACT.md`, `TEST-AND-QUALITY-SPEC.md`.

### SW-501 Owner shell + nav
Owner Home/Work/Trips/More + Rail/two-pane; logout; session-expiry intercept. Source: NAVIGATION/SCREEN-CATALOG. Tests: nav/back. 

### SW-502 Owner dashboard (operational overview)
Today's trips/work/money; active drivers/labourers; recent activity; alerts; pending approvals; quick access. Source: SWF-03, SCREEN-CATALOG O-Home. Data: org reads (approved rules). Tests: dashboard states.

### SW-503 Approvals screen
Approve/reject pending driver/labourer registration. Source: SWF-02; B-03. Security: owner only; audit. 

### SW-504 Manage Users / Drivers / Labourers / Tractors screens
CRUD driver/labourer/tractor records + user status/approval. Source: SWF-07/08 + role model. Rules: tractor registry OWNER-managed (init Sonalika/John Deere, not hardcoded); soft-delete. Security: owner; audit. 

### SW-505 Attendance & corrections (owner)
Working/absent day tracking; corrections with reason + audit; no silent overwrite. Source: SWF-08; B-06. Rules: R-20..; date-based history. 

### SW-506 Rates & money-rule settings screens
Owner configure rate (₹200 default) + distribution rule. Source: SWF-09/10/19; B-07/08. Preview. Security: owner. 

### SW-507 Settings screen
Summary time (19:30, 7-8 PM window), notification prefs, profiles, defaults. Source: SWF-19; B-17. Owner write; audit. 

### SW-508 Reports & Export (PDF/CSV) + multi-tractor reports
Owner-only; date range; breakdowns by all/each tractor, driver, labourer, date; money totals; working/absent days. Source: SWF-18; B-15; EXPORT-REPORT-SPEC. Blaze for cloud/Storage path; local PDF/CSV on Spark still owner-scoped. Tests: export authz, breakdown correctness, real-data only. 

### SW-509 Audit viewer (owner read)
Read-only server-authoritative audit log (filters, before/after). Source: SWF; CONCURRENCY-IDEMPOTENCY-AUDIT §Audit. Read-only; owner. 
