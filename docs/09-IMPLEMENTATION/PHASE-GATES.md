# PHASE GATES

A phase is COMPLETE only when its gate below passes. Code compiling is NOT completion. Any gate item that fails or is unverifiable → the phase is BLOCKED until resolved.

## Common quality gate (applies to every phase)
```
□ Required implementation complete (all tasks in phase COMPLETE with evidence)
□ No prohibited placeholders (AGENT §14.24 semantic + token scan)
□ No fake production data / fake backend / fake success
□ No incomplete required paths / no dead interactions
□ Tests for implemented behaviour pass (real assertions; no disabled/weakened gates)
□ Static analysis passes
□ Security checks relevant to the phase pass
□ Accessibility relevant to the phase passes
□ Acceptance criteria of the phase pass
□ Documentation + TRACEABILITY updated
□ No unresolved critical blocker; blockers are explicit & recorded
```

## Gate-0 — Readiness (before ANY implementation)
- IMPLEMENTATION STATUS READY (owner confirms scope S-V1; D-1/D-2/D-6 deferred-vs-v1 decided).
- D-4 package/applicationId/branding/signing resolved.
- D-3 legacy migration decided.
- Environment/credentials/signing available (not required to write this plan, required to execute).
- SEC-1 keystore remediation planned.
- DEPENDENCY-POLICY versions re-verified.
Result: BLOCKED until all pass. If unresolved → do not start.

## Gate-1 — Foundations complete
All IMPL-101..110 complete per their DoD; app builds; DI graph; nav scaffolds; theme; domain + R-rule use cases tested; local schema declared. SEC scan clean.

## Gate-2 — Data & offline layer complete
IMPL-201..204 complete; offline reads serve all owner queries; outbox idempotent replay tested; conflict model present; no fake success. Repository/DAOs unit-tested.

## Gate-3 — Auth & security foundation complete
IMPL-301..307 complete against a REAL Firebase env; rules/CF emulator tests pass (escalation/ownership/forge/deep-link/storage/timestamp/duplicate); audit server-generated; App Check on; no fake auth. (Cannot pass without env → env is a hard prerequisite.)

## Gate-4 — Owner core workflow complete
IMPL-401..408 complete; every core screen meets the 8-part sub-checklist (state/VM/repo/UI/states/a11y/responsive/tests); E2E owner daily flow (FE1/FE2) passes; attendance integrity + delete cascade audited.

## Gate-5 — Catalogues & reporting/backup complete
IMPL-501..507 complete (approved optional set); analytics/backup/export verified; restore rollback tested; no silent data loss.

## Gate-6 — Account/settings/secondary complete
IMPL-601..606 complete (approved optional set); destructive re-auth; profile self-only; audit viewer read-only.

## Gate-7 — Cross-cutting complete
IMPL-701..705 complete; accessibility PASS all applicable axes; responsive PASS per breakpoint; performance targets measured/confirmed; assets authoritative (no fabricated brand); localization externalised.

## Gate-8 — Release
IMPL-801..805 complete; full FINAL-TEST-CONTRACT suites green; AT-1..20 sign-off; perf macrobenchmark done; SEC-1 remediated; release build on clean machine + Play signing + PRE-RELEASE gate; Crashlytics/monitoring live; IMPLEMENTATION CONTROL final audit clean (orphans 0).
