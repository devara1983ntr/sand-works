# SAND WORKS — Implementation Control Plane (docs/09-IMPLEMENTATION)

Status: **RECONCILED to `docs/10-SANDWORKS/` (locked scope).** Planning-only — no code, no Firebase resources, no fake data. The former S-V1 single-owner 52-task plan is **superseded** and no longer authoritative.

## Source-of-truth (authority order)
1. User/owner decisions → `docs/10-SANDWORKS/DIRECTIVE-REGISTER.md`, `PRODUCT-FREEZE.md`
2. SAND WORKS spec → `docs/10-SANDWORKS/*` (roles, money engine, scheduling, security/RBAC, notifications, export, offline, UX, tests)
3. Binding rules → `docs/03-ENGINEERING/AGENT.md` §14 (immutable)
4. Retained reference → `docs/08-NATIVE-ANDROID/` (architecture/ADR continuity where non-conflicting)
5. Flutter reference app (`lib/`) → behavioural reference ONLY
6. Agent inference → never

> **No legacy single-owner assumptions apply.** Roles = OWNER/DRIVER/LABOURER (3, no admin). Money/rates/earnings/leaderboards/alerts/export/temp-assignment are IN scope. Package `com.roshan.sandworks`. Brand SAND WORKS, locked PNGs immutable (no SVG/regeneration). See `10-SANDWORKS` for the full contracts this plane executes.

## Readiness
Planning plane is READY **by scope**; go-live is gated by environment/business blockers (SW-BLK-1..6, A1/A2) that are documented, not disguised (see `DECISION-REGISTER.md` and `10-SANDWORKS/BLOCKERS.md`).

## Task inventory (reconciled) — see `tasks/PHASE-*.tasks.md`
| Phase | Task IDs | Count | Theme |
|---|---|---|---|
| 1 | SW-101..108 | 8 | Foundations & identity (project `com.roshan.sandworks`, DI, nav, design system, errors) |
| 2 | SW-201..204 | 4 | Data & offline (domain model, Room/outbox, repos, idempotency/conflict) |
| 3 | SW-301..309 | 9 | Auth, approvals, RBAC, Firestore/Storage rules, CF, audit, org scope |
| 4 | SW-401..404 | 4 | Money engine & scheduling (rates, distribution, daily closure, leaderboards) |
| 5 | SW-501..509 | 9 | OWNER surfaces |
| 6 | SW-601..605 | 5 | DRIVER surfaces |
| 7 | SW-701..704 | 4 | LABOURER surfaces |
| 8 | SW-801..803 | 3 | Notifications & alerts infrastructure |
| 9 | SW-901..906 | 6 | Quality gates & release |
| **Total** | | **52** | |

Mappings, gates, dependency/critical path, traceability, risks, decisions, change-control, progress and completion are in the sibling files.
