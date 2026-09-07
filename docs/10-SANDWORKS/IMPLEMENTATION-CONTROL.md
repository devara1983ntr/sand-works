# IMPLEMENTATION CONTROL — SAND WORKS

Maps the locked SAND WORKS scope to the execution-control system in `docs/09-IMPLEMENTATION`. This directive **re-freezes** the product; the phase/task structure in `09-IMPLEMENTATION` is retained as the mechanism but its task inventory, decision register and traceability MUST be reconciled to this SAND WORKS scope (roles + money/rates/leaderboard/alerts/export + Blaze gating). No code is written until execution is READY.

## Readiness
**Planning READY per scope.** Execution blockers (recorded, not disguised): 
- SW-BLK-1 real Firebase project + credentials/env.
- SW-BLK-2 Firebase Blaze-vs-Spark decision (gates Cloud Storage SWF-24, Cloud-scheduled closure SWF-25, Cloud backup SWF-26). If Spark: implement documented fallbacks; never fake Storage/scheduling.
- SW-BLK-3 dedicated release signing identity (never legacy keystore; env/ignored creds; SEC-1 hygiene).
Non-blocking confirmations listed in DIRECTIVE-REGISTER.

## Scope → phase mapping (reconcile `09-IMPLEMENTATION` phase model to this)
| 09 Phase (re-derived) | SAND WORKS content |
|---|---|
| Phase 0 Gate | Confirm SW scope READY (env/Blaze/signing) |
| Phase 1 Foundations | Project `com.roshan.sandworks`, build/DI/nav/theme/logging/domain; design system (UX-DESIGN-SYSTEM); asset packaging FROM masters |
| Phase 2 Data & offline | Room cache+outbox+repositories; data model (DATA-MODEL); idempotency/conflict |
| Phase 3 Auth & security | Auth + approval + RBAC (ROLE/SECURITY), Firestore/Storage rules, App Check, CF B-ops, audit, org scope; Blaze-gated paths conditionally |
| Phase 4 Money & scheduling engine | Rate snapshots, distribution, earnings, daily closure idempotency, leaderboard (MONEY/SCHEDULING) + tests |
| Phase 5 Role workflows | Owner/Driver/Labourer screens (SCREEN-CATALOG); trip/attendance/approval/assignment |
| Phase 6 Notifications & alerts | Notification A–F + owner alert + WhatsApp share + messaging |
| Phase 7 Reporting/export/settings | PDF/CSV owner export, multi-tractor reports, settings |
| Phase 8 Cross-cutting + release | a11y, responsive, perf, assets verification, tests, signing, private-APK release |

## Task inventory (52 tasks reconciled to SW scope — to be re-derived in `09-IMPLEMENTATION` files)
Full per-task contracts live in `09-IMPLEMENTATION/tasks/`. Reconcile each task's Source-of-truth + feature to the SWF-*/SW-* IDs and screens here. Do NOT carry over S-V1 single-owner-only assumptions that conflict (driver/labourer are now roles; money/leaderboard/alerts now in scope). Mark role gates, Blaze gates, and approval/temp-assignment expiry in each affected task.

## Traceability requirement
Requirement (directive §1-47 / SWF-*) → Feature → Screen → Action → Use case → DB (DATA-MODEL) → Backend op → Authorization (SECURITY-RBAC) → Error/offline → Test (TEST-AND-QUALITY) → Task. Zero unexplained required features; zero orphan tasks; reconcile in `09-IMPLEMENTATION/TRACEABILITY-MATRIX.md`.

## Rules recap
- Build MUST/SHOULD (PRODUCT-FREEZE) only; OUT/DEFERRED never built silently.
- Real/truthful/secure/tested; AGENT §14 + §14.29 immutable; no placeholders/fake; blocker→report.
- Money integer; closure idempotent; no "payment" wording; no fake Storage/scheduling on Spark.
