# SAND WORKS — Implementation Master Plan

Status: **RECONCILED to `docs/10-SANDWORKS/` (locked scope).** Supersedes retired S-V1 plan. Planning only.

## Plan statement
Deliver a private, offline-first, role-based (OWNER/DRIVER/LABOURER) Android app **SAND WORKS** (`com.roshan.sandworks`) per the locked `10-SANDWORKS` spec. No public store release. Execution is gated by owner/env blockers SW-BLK-1..6, A1/A2; nothing here is disguised as a task.

## Work breakdown (52 tasks across 9 phases)
Refer to `README.md` grid and `tasks/PHASE-*.tasks.md` for the authoritative full contract per task (each carries Objective/Why/Source/Security/Offline/Conflict/Blockers/Tests/Acceptance/Status/Downstream). Summary of ownership class per task:

- **Data/domain/offline/UI/tests:** SW-101..108, SW-201..204, SW-401..404, SW-501..509, SW-601..605, SW-701..704, and quality SW-901..903 — buildable to READY once Gate-0 clears; they do NOT depend on Firebase creds at authoring time (but their acceptance of cloud-backed features does at runtime).
- **Backend/security/CF (real Firebase required):** SW-301..309, SW-801..803, and export/photo/release deps (SW-508, SW-605/SW-704 photos, SW-904) — **BLOCKED** until SW-BLK-1 (project+config), SW-BLK-2 (Blaze), SW-BLK-4 (FCM). These must NOT be faked; implement only what credentials permit, else STOP and report (AGENT §14.23).
- **Owner decision gating:** copy wording (SW-BLK-5), UX/wireframes (SW-BLK-6), Blaze plan (SW-BLK-2), canonical assets (SW-BLK-A1/A2), signing (SW-BLK-3).

## Critical path (dependency spine)
Gate-0 → P1(F1) → P2(F2) → P3(F3; **needs SW-BLK-1**) → P4(F4) → P5(F5 OWNER) ‖ P6(F6 DRIVER) ‖ P7(F7 LABOURER) → P8(F8; **needs SW-BLK-4**) → P9(F9; **needs SW-BLK-3/5/6**).
P5/P6/P7 are mutually parallelizable once P1–P4 land; all surface layers consume P2 repositories and P3 scoping/security.

## Workstreams (recommended lanes)
1. **Front-end foundation lane:** P1+P2 → enables all UI authoring off Firebase.
2. **Backend/security lane:** P3 (+SW-301 real project) → unblocks writes, CF, approvals.
3. **Money lane:** P4 on top of P2/P3.
4. **Surface lanes:** P5/P6/P7 (parallel), then P8 notifications, then P9 quality/release.
Single "owner" assumption is retired — each task names OWNER / DRIVER / LABOURER surfaces; backend tasks are role-agnostic infra.

## Inter-task data/flow
Writes (trips, rates, rules, labourer, approvals, assignments, corrections) flow client→outbox→(Rules)→Cloud Functions (server-authoritative: numbering, approval, rate/rule snapshot, closure, leaderboard, assignment expiry, alert, notifications, export, profile photo) → Firestore/Storage; all writes emit audit; all reads are server-rules-scoped; offline reads are local-first. Daily closure + notifications + cloud export are Blaze-dependent with documented Spark fallbacks (never fake).

## Execution policy
- Build nothing requiring an unresolved blocker; author/execute to the boundary of what is specified.
- Every completed task flips its `Status` to `DONE` in `tasks/PHASE-*.tasks.md` and is recorded in `PROGRESS-TRACKER.md` → `COMPLETION-REGISTER.md`.
- Phase exit only via its Gate (`PHASE-GATES.md`).
