# DEPENDENCY MATRIX & CRITICAL PATH

Hard dependencies (H): downstream must not start until upstream COMPLETE. Soft (S): should be complete but not strictly blocking. This matrix is authoritative for task ordering.

## Phase dependency graph
```
Gate-0 (READY)
   │
   ▼
Phase 1 (IMPL-101..110)  ──► Phase 2 (201..204)  ──► Phase 3 (301..307)  ──► Phase 4 (401..408)
                                                                              │
                                             ┌───────────┬───────────────────┼──────────────┐
                                             ▼           ▼                   ▼              ▼
                                       Phase 5 (501..507)  Phase 6 (601..606)   Phase 7 (701..705)
                                             └───────────┬───────────────────┴──────────────┘
                                                         ▼
                                                  Phase 8 (801..805)  ──► RELEASE
```
Phase 3 requires REAL Firebase env (hard env prerequisite). Phase 4 consumes Phase 2 (local/offline) and Phase 3 (rules/CF/audit).

## Key hard links (examples)
- IMPL-101 → IMPL-104,105,106,107 (foundation before use)
- IMPL-110/201 (schema) → IMPL-202 repositories → IMPL-203/204
- IMPL-302 auth → IMPL-303 provisioning → IMPL-401 auth screens
- IMPL-304 rules + IMPL-305 CF → every write screen (405/406/407/501…)
- IMPL-204 conflict → IMPL-406 attendance conflict + 405 number race
- IMPL-108/109 domain → Phase 2 repositories → Phase 4
- IMPL-304/305 (rules/CF B-*) → IMPL-406 (attendance CF), IMPL-506 (backup), IMPL-604 (notif B-14)

## Critical path (drives release)
Gate-0 → IMPL-101 → 104 → 106 → 108 → 109 → (Phase 2) 110/201 → 202 → 203/204 → (Phase 3) 301 env → 302 → 304/305 → 307 → (Phase 4) 401 → 402 → 403 → 404 → 405 → 406 → 407 → 408 → (Phase 5) 501 → 504 → 506 → (Phase 8) 801 → 802 → 803 → 804 → 805.
NOTE: Phase 3's rules/CF (304/305) sit ON the critical path before core write screens (405/406). A delayed env blocks 304/305 and hence the whole core — an unflagged schedule risk if env is late (see RISK-REGISTER).

## Parallel groups (safe if contracts fixed)
| Group | Tasks | Shared contract | Conflict risk | Integration point |
|---|---|---|---|---|
| Design system/theme | IMPL-107 || IMPL-106 nav | IMPL-105 logger | none | integrated in Phase 4 screens |
| Domain model + R-rule use cases | IMPL-108,109 | domain contract | low | consumed by repositories |
| DAO/schema + repositories | IMPL-201,202 | Room schema | shared schema must be frozen | sync engine IMPL-203 |
| Analytics data + backup data | IMPL-504 read-side, IMPL-506 | counters/backups contract | both touch CF B-05/11 | IMPL-305 |
| Optional screens | IMPL-503,505,604,605,606 | their own screens | isolated | integrated per feature |

Never parallelize tasks that mutate the same architectural contract without coordination (e.g., do not run IMPL-405/406 against an unfrozen rules/CF contract).
