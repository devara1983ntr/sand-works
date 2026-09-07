# Task Contract Template

Every task in `tasks/PHASE-*.tasks.md` MUST be written to this template. Fill every applicable field. "n/a" is allowed only where genuinely not applicable to the task (state why). Missing fields = task not ready for execution.

## Mandatory fields
```
TASK-ID:        (IMPL-###, global unique)
Title:
Phase:
Status:         (BLOCKED | READY | IN PROGRESS | COMPLETE | DEFERRED | CANCELLED)
Priority:       (P0..P4)
Type:           (foundation|domain|data|backend|security|ui|testing|release|asset|infra)
Objective:      (one sentence: what this delivers)
Why this exists:(trace to requirement/decision; why the app needs it)
Source-of-truth:(exact doc:file + IDs — e.g. 08-NATIVE-ANDROID/FINAL-FEATURE-CATALOG.md FV-11; FINAL-BUSINESS-RULES.md R-10)
Prerequisites:  (task IDs / phase gates that must be complete)
Dependencies:   (hard/soft)
Inputs:         (artefacts it consumes)
Expected files: (exact modules/files/components to produce)
Expected implementation: (what real behaviour/code; what is explicitly NOT to do)
Business rules: (R-IDs that apply)
Conditional logic:(C-ID branches that must hold)
Security requirements: (authz/AUTHZ-OWNER, server-authority, audit, no client-trust)
Data requirements: (collections/fields/entities per FINAL-DATABASE-SCHEMA)
UI/UX requirements: (screen, components, design-system)
Error states:   (per FINAL-ERROR-CONTRACT)
Loading states: (per FINAL-UI-STATE-CONTRACT)
Empty states:
Offline states: (per FINAL-OFFLINE-SYNC; no fake success)
Accessibility:  (per FINAL-ACCESSIBILITY gate)
Performance:    (per FINAL-PERFORMANCE-CONTRACT)
Tests required: (FT- suites/tests)
Acceptance criteria: (measurable; maps to DoD)
Definition of Done:  (all of: real impl, states, validation, persistence/backend, authz, loading/empty/error/offline/conflict, a11y, tests, no placeholders/fake/dead paths)
Known risks:
Possible blockers:   (incl. any BLOCKED - category)
Out-of-scope items:  (explicitly what this task must NOT include)
Evidence required:   (tests/logs/diff/emulator output to submit)
Downstream tasks:    (task IDs that consume this)
```

## DoD gate (task-level, from AGENT §14.22/14.24/14.25)
Complete only when all of the above true: real implementation exists; state transitions real; validation; persistence/backend integration where required; authorization; truthful loading/success/failure/offline/conflict; accessibility; tests cover critical behaviour; no prohibited placeholders; no dead required interactions; no known incomplete paths; pre-commit semantic scan clean.

## Blocker handling (AGENT §14.23)
If the task cannot be truthfully completed: set Status=BLOCKED, record in `TASK-MANAGEMENT.md` blocker protocol + `DECISION-REGISTER.md`. NEVER fabricate/stub/skip/mark complete.
