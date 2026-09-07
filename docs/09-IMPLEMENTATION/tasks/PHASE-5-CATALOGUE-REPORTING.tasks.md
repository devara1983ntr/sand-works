# Phase 5 — Catalogues & Reporting/Backup — Task Contracts

Prerequisites: Phase 4 (Gate-4). Execution BLOCKED until READY. Sub-checklist in PHASE-4 header applies to every screen task.

Shared refs: `FINAL-SCREEN-CATALOG.md`, `FINAL-BUSINESS-RULES.md`, `FINAL-BACKEND-CONTRACT.md`, `FINAL-FEATURE-CATALOG.md`.

### IMPL-501 Labourers catalogue (N-29)
Objective: add/edit/soft-delete/inactivate labourers (name preserved exact, optional phone), org-scoped.
Source: FV-20; R-50/51; B-07. Data: labourers. Forms: FV-G. Tests: FT-C1. Sub-checklist: full.

### IMPL-502 Drivers catalogue (N-31)
Objective: driver records CRUD + vehicle linkage + active.
Source: FV-21; R-50/51; B-07. Data: drivers. Tests: FT-C2. Sub-checklist: full.

### IMPL-503 Vehicles/Tractors (N-30) — V1 OPTIONAL (S2)
Objective: vehicle master. Source: FV-22. If this optional is not approved, mark Status=DEFERRED (approved decision only), do NOT build silently. Sub-checklist: full.

### IMPL-504 Analytics/Reports (N-27)
Objective: KPIs (works/trips/top driver) + sortable table + grouped history (FR-14 parity), owner-scoped, not full-history client scan (PERF; CF counters).
Source: FV-30; FR-14/UC-8; R-70. Data: counters/trips. Tests: FT-D1. Sub-checklist: full.

### IMPL-505 CSV export (N-28) — V1 OPTIONAL (S1)
Objective: export filtered owner data to signed CSV via CF + Storage (B-13).
Source: FV-31; B-13; R-70/80. Tests: FT-D2. If not approved → DEFERRED.

### IMPL-506 Local backup/restore (N-38)
Objective: encrypted+signed `.labourbackup`; validate/snapshot/rollback/verify; no partial overwrite of newer cloud.
Source: FV-40; R-80/81; B-11/12; FR-15/16. Tests: FT-E1/FT-CONC. Sub-checklist: full.

### IMPL-507 Cloud backup/restore (N-38) — V1 OPTIONAL (owner confirmation)
Objective: durable cloud copy/restore via owner account (B-11/12). Depends real env (Phase 3). If not approved → DEFERRED. Tests: FT-E2.
