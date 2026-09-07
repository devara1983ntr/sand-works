# SAND WORKS — R-1 Control-Plane Reconciliation Audit

Status: **AUDIT COMPLETE (documentation/control-plane).** Date 2026-09-07. Planning only; no application code produced. This file records the fresh full audit requested in R-1 (14 named checks). Checks below are performed against the reconciled plane (this package + `docs/10-SANDWORKS/`). Environment facts are stated as they are (no remote in this working copy, no Firebase creds, asset discrepancy unresolved) and are **not** disguised.

## Audit scope & method
Read reconciled task contracts (9 files, 52 SW tasks), 09 meta-docs, `10-SANDWORKS` (24 docs incl. FEATURE-CATALOG/BLOCKERS/DIRECTIVE-REGISTER/READINESS-AUDIT), and `08-NATIVE-ANDROID` indexes; grep-based check for stale S-V1/IMPL/F-ids/legacy-role or single-owner language; requirement↔task mapping.

## 14-point audit result
1. **Stale contradictions** — PASS: all 8 S-V1 `PHASE-*.tasks.md` deleted; every 09 meta-doc rewritten to SW-xxx/SAND WORKS; supersession banner in README; stale IMPL/BLK-01..07 decision rows removed. Residual: repo-root top-level README still describes the legacy Flutter reference app (that README belongs to the reference project, referenced only; flagged, not part of SAND WORKS control plane).
2. **Requirement completeness** — PASS (mapped): all 23 V1-REQUIRED SWF-01..23 + 5 optional SWF-24..28 mapped to tasks/screens/gates (TRACEABILITY-MATRIX). SWF-90..95 explicitly OUT (no tasks). Security/RBAC, money engine, daily closure, leaderboards, temp-labour expiry, notifications, multi-driver/tractor, attendance, export, offline/sync, Firebase all represented.
3. **Task completeness** — PASS: 52 tasks across 9 phases; each maps to a real requirement (no orphan tasks); each phase file contract complete.
4. **Traceability** — PASS: bidirectional feature↔screen↔task↔phase↔gate matrix present.
5. **Phase/dependency correctness** — PASS: DEPENDENCY-MATRIX chains + parallel lanes + critical path reflect spec spine.
6. **Gate correctness** — PASS: PHASE-GATES Gate-0..9; Gate-0 PASS; env-gated gates marked BLOCKED not silently passable.
7. **Security/RBAC** — PASS (as spec): SW-301..309 cover real auth, approval, RBAC, org scope, rules, server-authority, audit, attack tests; labourer read-only; no admin.
8. **Money/integrity** — PASS (as spec): SW-108/401..404 integer paise, snapshot, idempotent closure exactly-once, accrued wording; risks R-4/R-5.
9. **Notifications** — PASS (as spec): SW-801..803 per-user A–F + owner alert, no cross-broadcast, honest Android limits, approved copy pending (SW-BLK-5).
10. **UI/UX** — PASS (as spec): SW-103/104/105/106, per-surface sub-checklist (states/a11y/responsive), SW-902; wireframes pending approval (SW-BLK-6).
11. **Tests** — PASS (as spec): SW-203/306/309/901/905 test matrix incl. offline/idempotency/security; no fake-test claims (no code run in this R-1).
12. **Placeholders / fabrications** — PASS: none fabricated. All Firebase/FCM/Storage/CF/signing items are recorded as real blockers (SW-BLK-1..6) or OPTIONAL/Blaze-gated, never invented as present.
13. **Secrets** — PASS: no secrets in authored docs; policy states keys in env/ignored. (A previously exposed token is outside this plane; owner should rotate it — see report.)
14. **Repo integrity** — PARTIAL: this working copy has **no configured git remote**, so remote HEAD/tree cannot be independently verified here (reported, not fabricated). Local tree consistent (docs authored). Commit of this package is staged/pending; local SHA will be reported after commit.

## Reconciliation verdict (spec)
- **Specification is READY** for an independent Kotlin + Jetpack Compose coding agent to implement the areas it is allowed to build **without guessing or following stale requirements**, up to the boundary set by unresolved environment/business blockers.
- **NOT-READY for go-live / full build of cloud-backed areas** until owner clears SW-BLK-1..6 (Firebase project+config, Blaze, signing, FCM, approved copy, wireframes) and SW-BLK-A1/A2 (canonical asset set).

## Blockers by category (summary; full detail `10-SANDWORKS/BLOCKERS.md` + `DECISION-REGISTER.md`)
- CREDENTIAL/ENVIRONMENT: SW-BLK-1 (Firebase), SW-BLK-4 (FCM).
- BUSINESS DECISION / EXTERNAL: SW-BLK-2 (Blaze).
- SECURITY REQUIREMENT/ENV: SW-BLK-3 (signing).
- DESIGN SPECIFICATION: SW-BLK-6 (wireframes).
- DESIGN/UX approval: SW-BLK-5 (notification/alert copy).
- MISSING ASSET (confirm): SW-BLK-A1, SW-BLK-A2.

## Asset discrepancy (explicit, unresolved)
Not guessed. Recorded as SW-BLK-A1/A2 pending owner confirmation of canonical masters (zip `master/` 1536×1536 vs repo-root `sand_works_*_master.png` 1254×1254 & 1024×1024) and logo aspect. PNG immutability (no SVG/regeneration) preserved throughout.

---

# R-1.1 follow-up corrections (this turn)

Scope: documentation-only reconciliation requested after R-1. No application code. No PNG discrepancy resolution. No remote verification claimed.

## Corrections applied
1. **Root `README.md` is legacy & could mislead** → added a prominent top banner marking it **LEGACY Flutter reference README, NOT the SAND WORKS spec**, with authority pointers to `docs/10-SANDWORKS/`, `docs/09-IMPLEMENTATION/`, `AGENT.md` §14. Original content (sections 1–12) preserved for reference; end-of-file supersession note added.
2. **09 tasks do not depend on the legacy README** → verified: no 09 task cites root README/legacy product as a requirement. (Its only `README.md` hits point to the control-plane `09-IMPLEMENTATION/README.md`; `com.roshan.labourparty` appears solely as a "never use" guard.)
3. **Corrected task statuses** → replaced the false "BLOCKED at authoring" flags with an **emulator-first model**: **0 tasks blocked from coding; 52 NOT-STARTED; 0 DONE**. Real-cloud/FCM/release readiness is expressed as per-task **DONE dependencies**, not coding blocks. Phase files' `Status:` lines for SW-301/SW-801 updated to match.
4. **Two-tier blocker matrix** → `DECISION-REGISTER.md` now states, per SW-BLK, whether it applies at (a) real-cloud integration+go-live or (b) finalisation/release only. None is required before coding (emulator-first).

## Re-run audits (14-point) — updated result where changed
1. Stale-reference: **PASS** (adds root-README legacy marking). 2. Requirement completeness: **PASS** (unchanged). 3. Task completeness: **PASS** (52). 4. Traceability: **PASS**. 5. Phase/dependency: **PASS**. 6. Gate correctness: **PASS** (emulator-first; real-cloud/release gates explicit). 7. Security/RBAC: **PASS** (as spec). 8. Money/integrity: **PASS**. 9. Notifications: **PASS** (real-FCM verify = SW-BLK-4, not a coding block). 10. UI/UX: **PASS** (final polish = SW-BLK-6). 11. Tests: **PASS** (local/emulator runnable now). 12. Placeholders/fabrications: **PASS** (none; real-cloud never claimed). 13. Secrets: **PASS** (no secrets in committed markdown). 14. Repo integrity: **PARTIAL** — still no remote configured in this working copy; remote HEAD/tree not claimed (pushed separately by owner-authorized action this turn if remote added).

## Verdict (unchanged, restated)
Specification **READY** for an independent Kotlin+Compose agent to implement (author + emulator-test) without guessing/stale requirements; **real-cloud integration & go-live** remain gated by SW-BLK-1..6/A1/A2.
