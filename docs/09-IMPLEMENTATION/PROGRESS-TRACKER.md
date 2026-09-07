# SAND WORKS — Progress Tracker

Status: **RECONCILED to `docs/10-SANDWORKS/`.** Planning-only. **No implementation started** (R-1/R-1.1 are documentation/control-plane reconciliation). This file (with each task's contract) is the source of execution truth for task state.

## Status model (R-1.1 corrected — emulator-first)
A task's **Status** answers: *"can an independent coding agent begin implementing this now (author code + verify against local unit/UI tests and the local Firebase Emulator Suite / debug builds)?"*
- **NOT-STARTED** = specified & implementable now; not yet begun. No task is blocked from being coded: the **Firebase Emulator Suite** (Auth/Firestore/Storage/Functions) covers backend verification locally, debug builds need no signing, and FCM/UI flows can be authored with local/in-app fallbacks.
- **DONE dependency** (separate column) = the owner/environment input (SW-BLK-1..6, A1/A2) required before that task's **real-cloud integration, release, or verified-DONE** is met. This is a **finish/go-live gate, NOT a coding gate** — captured in the two-tier blocker matrix (see `DECISION-REGISTER.md`).

Consequence: **0 tasks BLOCKED at authoring; 52 NOT-STARTED; 0 DONE.** Real-cloud/release readiness is expressed via the DONE-dependency column and blockers, never by a false coding block.

### Phase 1 — Foundations & identity (Gate-1)
| Task | Title | Status | DONE dependency |
|---|---|---|---|
| SW-101 | Project `com.roshan.sandworks`, tooling, brand wiring | NOT-STARTED | release-signing only at SW-904 |
| SW-102 | Dependency & version catalog | NOT-STARTED | — |
| SW-103 | App config & secrets handling | NOT-STARTED | real `google-services` used at SW-301 |
| SW-104 | Hilt DI foundation | NOT-STARTED | — |
| SW-105 | Logging & typed errors | NOT-STARTED | — |
| SW-106 | Navigation & deep-link model | NOT-STARTED | auth re-validation at P3 |
| SW-107 | Money & time primitives (integer ₹, IST) | NOT-STARTED | — |
| SW-108 | Money engine domain logic | NOT-STARTED | server/CF re-validation at P3 |

### Phase 2 — Data & offline (Gate-2)
| Task | Title | Status | DONE dependency |
|---|---|---|---|
| SW-201 | Room schema, migrations, DAOs, DataStore, outbox | NOT-STARTED | — |
| SW-202 | Repository contracts + local-first impls | NOT-STARTED | — |
| SW-203 | Outbox + sync + idempotency + backoff | NOT-STARTED | real-cloud sync verify at P3 |
| SW-204 | Optimistic concurrency (`rev`) + conflicts | NOT-STARTED | — |

### Phase 3 — Auth, security, RBAC, rules, CF, audit (Gate-3)
| Task | Title | Status | DONE dependency |
|---|---|---|---|
| SW-301 | Firebase bootstrap + App Check (real project) | NOT-STARTED | **SW-BLK-1** (real project); SW-BLK-2 for Play Integrity/cloud |
| SW-302 | Auth integration + session | NOT-STARTED | SW-BLK-1 (real auth verify) |
| SW-303 | Owner provisioning (CF B-01) | NOT-STARTED | SW-BLK-1 (+ SW-BLK-2 if Blaze) |
| SW-304 | Driver/labourer registration + approval (B-02/03) | NOT-STARTED | SW-BLK-1 |
| SW-305 | RBAC client gating + org scope + deep-link | NOT-STARTED | — (rules live at SW-306) |
| SW-306 | Firestore/Storage rules + emulator tests | NOT-STARTED | SW-BLK-1/2 (deploy live) |
| SW-307 | Cloud Functions B-01..18 | NOT-STARTED | SW-BLK-1/2 (deploy live) |
| SW-308 | Server-authority + audit integration | NOT-STARTED | SW-BLK-1 |
| SW-309 | Security attack tests (AT) | NOT-STARTED | SW-BLK-1 (full live suite) |

### Phase 4 — Money engine & scheduling (Gate-4)
| Task | Title | Status | DONE dependency |
|---|---|---|---|
| SW-401 | Rate config + per-trip snapshot | NOT-STARTED | SW-BLK-1 (CF B-07 live) |
| SW-402 | Distribution config + calc | NOT-STARTED | SW-BLK-1 (CF B-08 live) |
| SW-403 | Daily closure/earnings + scheduling | NOT-STARTED | SW-BLK-2 for Blaze cloud-schedule path (Spark fallback implementable now) |
| SW-404 | Weekly/monthly leaderboards | NOT-STARTED | SW-BLK-1 (CF B-10 live) |

### Phase 5 — OWNER surfaces (Gate-5)
| Task | Title | Status | DONE dependency |
|---|---|---|---|
| SW-501 | Owner shell + nav | NOT-STARTED | — |
| SW-502 | Owner dashboard | NOT-STARTED | — (analytics SWF-27 needs SW-BLK-1) |
| SW-503 | Approvals screen | NOT-STARTED | SW-BLK-1 (CF B-03 live) |
| SW-504 | Manage users/drivers/labourers/tractors | NOT-STARTED | SW-BLK-1 |
| SW-505 | Attendance & corrections | NOT-STARTED | SW-BLK-1 (CF B-06 live) |
| SW-506 | Rates & money-rule settings | NOT-STARTED | SW-BLK-1 |
| SW-507 | Settings (summary time, profiles, backup) | NOT-STARTED | cloud backup SWF-26 needs SW-BLK-2 |
| SW-508 | Reports & Export PDF/CSV | NOT-STARTED | cloud/Storage export SWF-18 needs SW-BLK-2 (local PDF/CSV now) |
| SW-509 | Audit viewer (owner read) | NOT-STARTED | SW-BLK-1 (live audit data) |

### Phase 6 — DRIVER surfaces (Gate-6)
| Task | Title | Status | DONE dependency |
|---|---|---|---|
| SW-601 | Driver shell + nav | NOT-STARTED | — |
| SW-602 | Driver dashboard (+ ADD TRIP) | NOT-STARTED | — |
| SW-603 | Driver add/edit trip + numbering | NOT-STARTED | SW-BLK-1 (CF B-04/05 numbering live; emulator-tested now) |
| SW-604 | Driver my-totals + WhatsApp share | NOT-STARTED | SW-BLK-1 (real totals source) |
| SW-605 | Driver profile (+photo) | NOT-STARTED | photo via Storage SWF-24 needs SW-BLK-2 |

### Phase 7 — LABOURER surfaces (Gate-7)
| Task | Title | Status | DONE dependency |
|---|---|---|---|
| SW-701 | Labourer shell + nav (read-only) | NOT-STARTED | — |
| SW-702 | Labourer dashboard (personal metrics) | NOT-STARTED | — |
| SW-703 | Labourer history + leaderboard view | NOT-STARTED | SW-BLK-1 (live leaderboard) |
| SW-704 | Labourer profile + notifications | NOT-STARTED | photo SW-BLK-2; notifications P8 |

### Phase 8 — Notifications & alerts (Gate-8)
| Task | Title | Status | DONE dependency |
|---|---|---|---|
| SW-801 | FCM integration + token lifecycle | NOT-STARTED | **SW-BLK-4** + SW-BLK-1 (real FCM token/push verify) |
| SW-802 | Notification centre + types A–F + deep links | NOT-STARTED | SW-BLK-4 (real push); copy SW-BLK-5 |
| SW-803 | Owner alert/warning + ack | NOT-STARTED | SW-BLK-4 (real push); SW-BLK-5 (approved copy) |

### Phase 9 — Quality gates & release (Gate-9)
| Task | Title | Status | DONE dependency |
|---|---|---|---|
| SW-901 | Full test matrix + coverage gate | NOT-STARTED | — (run local/emulator) |
| SW-902 | Accessibility + responsive + i18n audit | NOT-STARTED | SW-BLK-6 (approved wireframes for final polish) |
| SW-903 | Real-device matrix + performance + offline field test | NOT-STARTED | real devices/env (go-live) |
| SW-904 | Build, signing, release | NOT-STARTED | **SW-BLK-3** (keystore) + SW-BLK-1 (Play/real) |
| SW-905 | Final RBAC/security/compliance retest | NOT-STARTED | release candidate + env |
| SW-906 | Release readiness + handover + close-out | NOT-STARTED | SW-BLK-1/2/3/4/5/6, A1/A2 |

## Roll-up
Total 52. **NOT-STARTED 52 · DONE 0 · BLOCKED-at-authoring 0.** Real-cloud integration (SW-301), FCM push (SW-801..803) and release/signing (SW-904) **cannot reach verified DONE** until their DONE dependencies clear — this is a go-live gate, not a coding block. **Planning plane is READY by scope; real-cloud/release is env/business-gated** (two-tier matrix in `DECISION-REGISTER.md`).
