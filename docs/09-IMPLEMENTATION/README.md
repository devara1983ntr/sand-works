# docs/09-IMPLEMENTATION — Implementation Control System

> **⚠️ PRODUCT RE-FREEZE (SAND WORKS).** The controlling specification has moved to **`docs/10-SANDWORKS/`** (locked directive: 3 authenticated roles OWNER/DRIVER/LABOURER, no admin, package `com.roshan.sandworks`, money/rates/earnings/leaderboards/alerts/export in scope, START FRESH, brand assets locked). This directory remains the execution-control **mechanism** (phases, gates, tasks, registers), but its task inventory, decision register, traceability and per-phase contracts MUST be **reconciled** to the SAND WORKS scope before execution. Do not apply earlier S-V1 single-owner-only assumptions where they conflict. See `docs/10-SANDWORKS/IMPLEMENTATION-CONTROL.md`.

Status: **PLANNING ONLY.** No application code is created here. This directory is the execution-control layer between the authoritative specification and the future coding agent. Actual coding may begin ONLY when the authoritative gate reports **IMPLEMENTATION STATUS: READY**. It is currently **BLOCKED** (see §Readiness below, `DECISION-REGISTER.md`, and `docs/10-SANDWORKS/DIRECTIVE-REGISTER.md`).

## Source-of-truth hierarchy (authoritative)
1. Explicit user decisions (none yet that clear the gate)
2. Approved product freeze → `08-NATIVE-ANDROID/PRODUCT-FREEZE.md` (recommended **S-V1** — NOT owner-confirmed)
3. Implementation contract → `08-NATIVE-ANDROID/IMPLEMENTATION-CONTRACT.md`
4. Architecture/ADR → `08-NATIVE-ANDROID/ADR/001..015` + architecture docs
5. Final requirements → `08-NATIVE-ANDROID/FINAL-*`
6. Phase audit findings → `docs/00-AUDIT-INDEX.md`, phase 0/0.5/0.75 set
7. Existing verified implementation → Flutter `lib/` (behavioural reference; native app does not exist)
8. Agent inference — never a source of truth

When documents conflict: resolve by the authority rule, or record a blocker/decision. Never guess.

## Directory contents
| File | Purpose |
|---|---|
| `README.md` (this) | Purpose, authority, readiness, how to use |
| `IMPLEMENTATION-MASTER-PLAN.md` | Programme objectives, phase model, sequencing, counts |
| `IMPLEMENTATION-ROADMAP.md` | Ordered phase-by-phase task roadmap |
| `IMPLEMENTATION-RULES.md` | Binding execution rules (no-code-until-ready; AGENT §14 alignment) |
| `PHASE-GATES.md` | Per-phase completion gates |
| `TASK-MANAGEMENT.md` | Task taxonomy, statuses, small-step model, blocker protocol |
| `TRACEABILITY-MATRIX.md` | Requirement→…→Test→Task |
| `DEPENDENCY-MATRIX.md` | Hard/soft deps, critical path, parallel groups |
| `RISK-REGISTER.md` | Programme risks |
| `DECISION-REGISTER.md` | Open decisions/blockers (mirrors FINAL-DECISION-REGISTER) |
| `CHANGE-CONTROL.md` | Controlled-change process after go-live |
| `PROGRESS-TRACKER.md` | Live task counters (computed from actual task state) |
| `COMPLETION-REGISTER.md` | Evidence-based completion records |
| `tasks/TASK-CONTRACT-TEMPLATE.md` | The required per-task contract fields |
| `tasks/PHASE-*.tasks.md` | Per-phase full task contracts |

## Key counts (computed from the authoritative spec, not invented)
- V1 scope features to implement (FV-1..FV-70 V1 set): **24** (REQUIRED 18, OPTIONAL 6). DEFERRED 7 (FV-80..86), OUT OF SCOPE (FV-90 + listed) — recorded, not silently omitted.
- V1 screens with full spec rows: **25** (N-01…N-42 owner set). Driver/labourer screens N-11/N-50..N-63 deferred (D-1).
- Backend operations: **14** (B-01..B-14).
- Business rules: **27** (R-01..R-90).
- Security attack scenarios mapped: **20** (AT-1..AT-20) + cross-cutting security controls.
- Test suites in FINAL-TEST-CONTRACT: **17** categories.
- Implementation tasks (defined in `tasks/`): counted in `IMPLEMENTATION-ROADMAP.md`/`PROGRESS-TRACKER.md`.

## Readiness — IMPLEMENTATION STATUS: BLOCKED
Implementation must NOT begin. Blocking items (details + smallest decision needed in `DECISION-REGISTER.md`):
1. **Scope S-V1** not owner-confirmed (driver/labourer self-service, ADMIN, driver workflow in v1 or deferred?) — D-1/D-2/D-6.
2. **D-4** package/applicationId/branding/signing identity unresolved — blocks scaffold + release (plus audit SEC-1 keystore remediation).
3. **D-3** legacy data migration need unresolved.
4. **Environment**: real Firebase project + credentials + App Check + signing keys are NOT present (a sandbox cannot supply these). These are `BLOCKED — CREDENTIAL/ENVIRONMENT` and will block Phase 3 when reached; they are not disguised as tasks.
5. Dependency version policy requires re-verification on a Flutter-independent machine before use (`08-NATIVE-ANDROID/DEPENDENCY-POLICY.md`).

Until these clear, agents must follow §29 of the brief: **DOCUMENT → VERIFY → BLOCK IF NECESSARY**, never GUESS → CODE.

## How to use when READY
1. Read this README + IMPLEMENTATION-RULES + AGENT §14.
2. Select the next unblocked task from DEPENDENCY-MATRIX / ROADMAP.
3. Follow TASK-MANAGEMENT small-step model; use the task's full contract in `tasks/`.
4. On completion, run the quality gate in PHASE-GATES + AGENT §14.24/14.25; update PROGRESS-TRACKER + TRACEABILITY + COMPLETION-REGISTER.
