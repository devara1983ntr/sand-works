# IMPLEMENTATION ROADMAP

Ordered, dependency-aware task roadmap. **Execution is BLOCKED until the authoritative gate is READY** (see README + DECISION-REGISTER). Statuses: BLOCKED / READY / IN PROGRESS / COMPLETE / DEFERRED / CANCELLED.

Full per-task contracts: `tasks/PHASE-*.tasks.md`; contract fields: `tasks/TASK-CONTRACT-TEMPLATE.md`.

## Task inventory (52 tasks, computed from the task files)
| Phase | Task IDs | Count | Nature |
|---|---|---|---|
| 1 Foundations | IMPL-101..110 | 10 | skeleton, deps, config, DI, logging/errors, nav foundation, design system, domain model, business-rule use cases, local schema |
| 2 Data & offline | IMPL-201..204 | 4 | DAO/migrations/queries, repositories, outbox+WorkManager+idempotency, rev+conflict |
| 3 Auth & security | IMPL-301..307 | 7 | Firebase bootstrap, auth, provisioning, rules+emulator, CF B-01..14, server-authority+audit, RBAC/deep-link/AT |
| 4 Owner core workflow | IMPL-401..408 | 8 | auth screens, shell, dashboard, session/day/close, trip/numbering, attendance, delete/undo, history/search |
| 5 Catalogues+reporting/backup | IMPL-501..507 | 7 | labourers, drivers, vehicles(S2), analytics, CSV(S1), backup/restore, cloud(S1 opt) |
| 6 Account/secondary | IMPL-601..606 | 6 | profile, account/security, settings, notif(S4), help(S3), audit viewer(S5) |
| 7 Cross-cutting | IMPL-701..705 | 5 | a11y, responsive, performance, assets, localization |
| 8 Verification & release | IMPL-801..805 | 5 | full test suite, security sign-off, perf run, release gate, close-out |
| **Total** | | **52** | |

## Recommended execution order (critical path first)
Phase 1 → Phase 2 → Phase 3 (needs env) → Phase 4 → Phase 5 → Phase 6 → Phase 7 → Phase 8.
Within each phase: ascending task ID; respect DEPENDENCY-MATRIX hard links (e.g., IMPL-405→406; IMPL-406 needs IMPL-204 conflict + IMPL-304/305 rules/CF; IMPL-302→303→401; IMPL-305→ all write screens).

## Gate linkage
Phase COMPLETE only when its gate (PHASE-GATES.md) passes. A phase is not complete merely because it compiles. OPTIONAL (S1..S5) tasks may be DEFERRED only by explicit approval, not silently (PRODUCT-FREEZE governance).

## Blocked status
Currently the whole programme is BLOCKED at Phase 0 gate. Within it, IMPL-101/103/301/304/305 and IMPL-704 and Phase-8 have hard `BLOCKED — CREDENTIAL/ENVIRONMENT`/`— BUSINESS DECISION`/`— MISSING ASSET` blockers that are recorded, not disguised.
