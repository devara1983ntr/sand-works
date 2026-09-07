# Phase 6 — Account, Settings & Owner-secondary screens — Task Contracts

Prerequisites: Phase 4 (Gate-4). Execution BLOCKED until READY. Sub-checklist applies to each screen task.

Shared refs: `FINAL-SCREEN-CATALOG.md`, `FINAL-FORMS.md`, `FINAL-FEATURE-CATALOG.md`, `FINAL-BUSINESS-RULES.md`.

### IMPL-601 Owner profile (N-40)
Objective: displayName/phone/photo (self content only; role/email/org read-only).
Source: FV-50; R-09-profile; B-09; SERVER-AUTHORITY-MATRIX (self-limited). Photo via photo picker/Storage rules self. Tests: FT-F1. Sub-checklist: full.

### IMPL-602 Account & security (N-39)
Objective: change password (re-auth), delete-my-data (multiple confirm + re-auth + CF B-10 + audit + anonymize refs), data export, sign-out (with cache/outbox consent handling).
Source: FV-51; R-62/63; B-10. Security: destructive re-auth; ≥1 active owner guard. Tests: FT-F2/FT-SEC. Sub-checklist: full.

### IMPL-603 Business settings (N-37)
Objective: workType default/options, hints, session-boundary display, report flags; owner only; audit; rev conflict.
Source: FV-52; R-08-settings (enum/range); B-08. Data: settings. Tests: FT-F3. Sub-checklist: full.

### IMPL-604 Notification centre (N-41) — V1 OPTIONAL (S4)
Objective: owner system events (backup/restore/reminder), read state, deep link, offline read.
Source: FV-60; B-14; NOTIFICATION-COVERAGE (owner subset). If not approved → DEFERRED. Tests: FT-G.

### IMPL-605 Help/About (N-42) — V1 OPTIONAL (S3)
Objective: FAQ/version/support replacing inert reference tiles (real content or truthful; no fabricated claims — AGENT §14.13). If content undefined → BLOCKED — DESIGN SPECIFICATION. If not approved → DEFERRED.

### IMPL-606 Audit-log viewer (N-36) — V1 OPTIONAL (S5)
Objective: read-only owner-privileged audit viewer with filters/before-after.
Source: FV-70; AUDIT-LOG-INTEGRITY. Tests: FT-H1/FT-AUD. If not approved → DEFERRED.
