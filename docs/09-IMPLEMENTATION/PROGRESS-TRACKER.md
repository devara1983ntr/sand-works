# PROGRESS TRACKER

Live counts computed from actual task status in `tasks/PHASE-*.tasks.md`. Counts are recalculated each time a task changes status; percentages are derived only from real state (never asserted). Currently no tasks are executed (programme BLOCKED at Gate-0).

## Task counts by status (as of this file)
| Phase | IDs | BLOCKED | READY | IN PROGRESS | COMPLETE | DEFERRED | CANCELLED | Total |
|---|---|---|---|---|---|---|---|---|
| 1 Foundations | 101..110 | 101(env/decision),102(env),103(env),104(exec-block),105(exec-block),106(exec-block),107(exec-block),108(exec-block),109(exec-block),110(exec-block) | — | 0 | 0 | 0 | 0 | 10 |
| 2 Data & offline | 201..204 | exec-blocked (Gate-1) | — | 0 | 0 | 0 | 0 | 4 |
| 3 Auth & security | 301..307 | 301(env) + others exec-blocked | — | 0 | 0 | 0 | 0 | 7 |
| 4 Owner core | 401..408 | exec-blocked (Gates 2/3) | — | 0 | 0 | 0 | 0 | 8 |
| 5 Catalogue/reporting | 501..507 | exec-blocked | — | 0 | 0 | 0 | 0 | 7 |
| 6 Account/secondary | 601..606 | exec-blocked | — | 0 | 0 | 0 | 0 | 6 |
| 7 Cross-cutting | 701..705 | 704(asset) others exec-blocked | — | 0 | 0 | 0 | 0 | 5 |
| 8 Verification/release | 801..805 | exec-blocked + 804(env/identity) | — | 0 | 0 | 0 | 0 | 5 |
| **Total** | | **52** | — | **0** | **0** | **0** | **0** | **52** |

## Programme summary (computed)
- Total tasks: 52
- Completed: 0
- In progress: 0
- Blocked: 52 (all at/behind Gate-0; hard blockers: IMPL-101,102,103,301,304,305,704,804 + env/D-3/D-4)
- Deferred: 0 (optional S-items not yet approved; none silently dropped — see DECISION-REGISTER non-blocking)
- Cancelled: 0
- Remaining: 52
- Critical-path tasks (open): entire Phase 1→2→3→4→5→8 chain (unstarted)
- Failed gates: none attempted
- Open risks: RK-01..RK-15 (RISK-REGISTER)
- Open decisions/blockers: BLK-01..BLK-07 (DECISION-REGISTER)

## Rules
- Update this file whenever any task changes status.
- Percentages (if used) must equal (Completed / non-blocked total) from actual state; never claim partial progress on a blocked/unstarted critical path.
- Do NOT claim implementation progress; the programme is BLOCKED until Gate-0.
