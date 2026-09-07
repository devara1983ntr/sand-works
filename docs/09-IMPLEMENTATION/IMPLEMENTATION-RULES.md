# IMPLEMENTATION RULES (execution-layer, binding)

Complement `03-ENGINEERING/AGENT.md` (§14 Zero-Placeholder/§14.29) and the authoritative spec. These are the operational execution rules for the control system.

## R1 — No coding until READY
No application code is written until the authoritative gate reports `IMPLEMENTATION STATUS: READY`. Until then: DOCUMENT → VERIFY → BLOCK IF NECESSARY. Do NOT GUESS → CODE → DOCUMENT LATER.

## R2 — Source-of-truth hierarchy & authority
Follow README hierarchy + authority rule. Never resolve a contradiction by guessing — record a blocker/decision (DECISION-REGISTER.md).

## R3 — No fabrication, ever (AGENT §14)
No placeholders, fake data, fake backend, fake auth, fake success, dead UI, fabricated assets/copy, silent feature drop, architectural substitution, disabled quality gates. When a blocker is real, report it; do not disguise a blocked dependency as a task.

## R4 — Task discipline
- Execute only unblocked tasks whose prerequisites are COMPLETE (DEPENDENCY-MATRIX).
- One task at a time via the TASK-MANAGEMENT small-step model.
- Do not jump randomly between unrelated phases.
- Do not start a phase until its gate's prerequisites are met.
- Do not expand scope; DEFERRED/OUT-OF-SCOPE items are never built without explicit approval (CHANGE-CONTROL).

## R5 — Task contract completeness
A task is executable only when its contract (template) is filled. Any missing/ambiguous field on a critical path → treat as BLOCKED — DESIGN SPECIFICATION and report; never infer.

## R6 — Truthful states
Loading/Empty/Error/Offline/Submitting/Success/Conflict/Forbidden/Session-expiry must be real per FINAL-UI-STATE-CONTRACT + FINAL-ERROR-CONTRACT. No fake success; no fake loading; no fake synced.

## R7 — Security is not UI
Authorization enforced in Rules + Cloud Functions. UI hiding is not authorization. Never trust client role/status/security fields (SERVER-AUTHORITY-MATRIX).

## R8 — Completion & evidence
A task is COMPLETE only with evidence (tests/logs/diff/emulator output) recorded in COMPLETION-REGISTER. DoD per template + AGENT §14.22. A phase is COMPLETE only when its gate passes.

## R9 — Traceability
Every change keeps TRACEABILITY-MATRIX current; every implemented feature maps to an authoritative requirement; every task maps back; no orphan requirements/tasks.

## R10 — Change control
Frozen scope is controlled (CHANGE-CONTROL.md). New requirements go through impact/traceability/dependency/security/test/task/approval flow. Never silently alter frozen requirements.

## R11 — Progress honesty
PROGRESS-TRACKER counts are computed from actual task state; never claim completion of the critical path that is unfinished. No "90% done" while the critical path is open.

## R12 — Policy immutability
These rules + AGENT §14 are immutable during implementation. Changes require explicit user approval and the full quality re-audit (AGENT §14.29).
