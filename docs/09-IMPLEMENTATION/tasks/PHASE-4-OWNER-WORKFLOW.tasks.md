# Phase 4 — Owner Core Workflow — Task Contracts

Phase objective: deliver the V1 owner core features (auth/lifecycle screens, shell, dashboard, session/day, trip, attendance, delete, history) as real, fully-stated screens. Prerequisites: Phase 2 + Phase 3 (Gate-2/3); execution BLOCKED until READY.

## Mandatory screen sub-checklist (applies to EVERY screen task; nothing optional)
Each screen task is COMPLETE only when ALL of these are produced and gated:
1. State contract (sealed UiState per FINAL-SCREEN-STATE-MATRIX for that screen)
2. ViewModel (UDF; action→reaction per FINAL-ACTION-REACTION-MATRIX; no fake success)
3. Repository/data-source consumption (local-first; CF for privileged writes)
4. UI components (design system; real behaviour; no dead controls/placeholder UI)
5. States: Loading / Empty / Error / Offline / Submitting / Conflict (where applicable) / Unauthorized / Forbidden / Session-expiry — truthful (FINAL-UI-STATE-CONTRACT)
6. Accessibility gate per FINAL-ACCESSIBILITY (PASS all applicable axes)
7. Responsive/adaptive per FINAL-RESPONSIVE
8. Tests: FT-UI/FT-NAV/FT-E2E for that screen (Happy/Empty/Loading/Error/Offline/Forbidden + critical interaction)
Splitting the sub-checklist into discrete commits by the executing agent is encouraged; none may be skipped.

Shared refs: `FINAL-SCREEN-CATALOG.md`, `FINAL-SCREEN-STATE-MATRIX.md`, `FINAL-ACTION-REACTION-MATRIX.md`, `FINAL-UI-STATE-CONTRACT.md`, `FINAL-ERROR-CONTRACT.md`, `FINAL-WIREFRAMES.md`, `FINAL-FORMS.md`, `FINAL-GESTURES.md`, `FINAL-FORMS.md`.

---

### IMPL-401 Auth/lifecycle screens (N-01,N-03,N-04,N-05,N-06,N-07,N-08)
Title: Implement splash, login, forgot/reset, disabled, provisioning, session-expiry screens
Objective: real auth lifecycle UI (grouped; sub-checklist each).
Why: FV-1..4 auth journey.
Source: `FINAL-FEATURE-CATALOG.md` FV-1..4; `FINAL-NAVIGATION.md`; `FINAL-FORMS.md` FV-A/B/C.
Business rules: R-60..63. Security: no enumeration; no fake auth; role owner-only.
Forms: login/provision/reset (FV-A/B/C). Tests: FT-AUTH/FT-A1/A2/A4.
Sub-checklist: full.
Blockers: depends IMPL-302/303. Downstream: IMPL-402.

### IMPL-402 Owner shell (N-10)
Title: Owner shell + bottom nav/Rail + Back contract + logout
Objective: container navigation (Dashboard/Work/Reports/More), adaptive nav, session-expiry intercept, logout confirm.
Source: `FINAL-NAVIGATION.md`, `FINAL-SCREEN-CATALOG.md` SSC, `FINAL-RESPONSIVE.md`.
Security: logout clears cache/outbox per consent; disable clears cache. Tests: FT-NAV.
Sub-checklist: full.

### IMPL-403 Dashboard (N-20)
Title: Today dashboard + search + add + pull-refresh
Objective: today's sessions/trips, search, empty/offline/error, FAB add.
Source: FV-13, FR-02/UC-1/4.
Business rules: session model D-5. Data: workSessions+trips (Q-1/2/3). Tests: FT-B4/FT-UI.
Sub-checklist: full. Downstream: IMPL-404/405.

### IMPL-404 Work session create/open + day detail + close (N-24,N-22)
Title: Session editor + day/session detail + close op
Objective: create/open session (uniqueness), day detail grouping Morning/Evening, close with audit.
Source: FV-10/FV-16; R-01/R-40; B-02/B-05.
Business rules: R-01..03,R-40. Conditional: C-W1/C-D3. Forms: FV-D.
Data: workSessions + meta guard. Tests: FT-B1/B7/FT-CONC. Sub-checklist: full.

### IMPL-405 Trip editor + numbering + next-trip (N-25)
Title: Trip add/edit, server number allocation, next-trip copy, draft autosave
Objective: real trip recording with server-authoritative number and copy-last-trip prefill.
Source: FV-11; R-10..13; B-03; FR-06/07/08.
Business rules: R-10..13. Forms: FV-E. Offline: queue+pending (number hint). Concurrency: number race → CF re-derive.
Tests: FT-B2/FT-CONC/FT-OFFLINE. Sub-checklist: full. Downstream: IMPL-406.

### IMPL-406 Attendance (N-23)
Title: Attendance toggles, add/edit/remove labour (undo), correction with reason + history integrity
Objective: append-immutable attendance; corrections require reason + audit; no silent overwrite.
Source: FV-12; R-20..23; `ATTENDANCE-INTEGRITY.md`; B-04.
Business rules: R-20..23, R-33. Data: attendance + attendanceHistory. Security: CF record/correct; audit.
Tests: FT-B3/FT-AUD. Conflict: rev. Partial-failure handling. Sub-checklist: full.

### IMPL-407 Delete/undo (N-20,N-22,N-23)
Title: Soft-delete trip/session/catalogue with confirm, cascade, undo, audit
Objective: soft-delete per R-30/31 preserving history + numbering invariants.
Source: FV-15; R-30..33; B-06.
Tests: FT-B6/FT-AUD. Sub-checklist: full.

### IMPL-408 History + full search (N-27/history)
Title: Grouped history browse + search across history
Objective: browse date→session→trip; search across history (owner scope, rule-valid).
Source: FV-14; FR-13/UC-7; Q-7.
Tests: FT-B5. Sub-checklist: full.
