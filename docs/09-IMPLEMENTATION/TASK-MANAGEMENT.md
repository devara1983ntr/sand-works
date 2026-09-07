# SAND WORKS — Task Management (operating procedure)

Status: **RECONCILED to `docs/10-SANDWORKS/`.** Planning-only plane.

## Task identity
Canonical task IDs are **SW-xxx** (SW-101..SW-906 across 9 phases). Retired IMPL-xxx / S-V1 ids are void. Each task has a contract in `tasks/PHASE-<N>-<THEME>.tasks.md` with the template fields (Objective, Why, Source, Security, Offline, Conflict, Blockers, Tests, Acceptance, Status, Downstream).

## Task lifecycle
1. **NOT-STARTED** — specified, awaiting start.
2. **IN-PROGRESS** — set when authoring/implementation begins (execution plane).
3. **READY** — spec complete; buildable on Gate-0 / upstream clearance (current state for non-cloud tasks).
4. **BLOCKED** — depends on an un-cleared blocker (owner/env); reason = SW-BLK-* recorded. Never fabricate past it.
5. **DONE** — contract Status=DONE, exit gate passed, evidence in `COMPLETION-REGISTER.md`.

## Start / parallelisation rules
- Start a task only when its prerequisites (`DEPENDENCY-MATRIX.md`) and gate are satisfied (or it is explicitly parallel-safe per MASTER-PLAN lanes).
- P5/P6/P7 surfaces may be worked in parallel once P1–P4 clear.
- A task whose cloud acceptance needs a blocker (e.g. SW-603 CF numbering) may be authored to the boundary but may not claim cloud-backed acceptance until the blocker clears.
- When a blocker is met mid-task, STOP that sub-area and report (AGENT §14.23); do not fabricate a stand-in.

## Review & sign-off
- Per-screen sub-checklist must pass before a surface task is DONE (see Phase-5 header: states, a11y, responsive, tests).
- A task is DONE only with verifier + date (COMPLETION-REGISTER template).

## Ownership
Tasks are not single-owner: surface tasks identify the OWNER/DRIVER/LABOURER role they serve; backend/security/data tasks are role-agnostic infra for all three roles. Retired single-owner assumption does not apply.
