# RISK REGISTER

Programme risks with likelihood/impact/mitigation/owner. Reassess at every gate. Cross-references: `SECURITY-ATTACK-REVIEW.md` (AT-*), `08-NATIVE-ANDROID/PRODUCT-GAP-REGISTER.md` (G-*).

| ID | Risk | L | I | Mitigation | Owner |
|---|---|---|---|---|---|
| RK-01 | Scope not confirmed → build wrong V1 | H | H | Gate-0 BLOCKED until S-V1 + D-1/2/6 owner sign-off; PRODUCT-FREEZE binding | Product |
| RK-02 | D-4 identity unresolved blocks scaffold/release | H | H | Resolve before IMPL-101/804; throwaway id in private branch only, never ship | Product |
| RK-03 | No Firebase env/credentials/keys → Phase 3 & core blocked | H | H | Surface env blocker up front (not a task); provisioning runbook; no fake backend (AGENT §14.4) | DevOps |
| RK-04 | D-3 legacy migration wrongly assumed | M | M | BLOCKED until decision; no silent migration | Product |
| RK-05 | Dependency versions drift / undocumented deps | M | M | DEP policy re-verify on Flutter-independent machine; only approved deps (R3) | Eng |
| RK-06 | Rules/CF delivered late stalls core (on critical path) | M | H | Build rules/CF early (Phase 3); env ready in parallel | Security/DevOps |
| RK-07 | Offline no-fake-success regression (false "synced") | M | H | OFFLINE contract + outbox tests (FT-OFFLINE); UI-state review | Eng/QA |
| RK-08 | Concurrency (rev/idempotency) incomplete → dup/lost writes | M | H | CONCURRENCY-SPEC tests; CF idempotency; conflict UI | Eng |
| RK-09 | Attendance history silently overwritten | M | H | ATTENDANCE-INTEGRITY append model + FT-AUD | Eng |
| RK-10 | Client-trusted role/status escalation | L | H | SERVER-AUTHORITY + rules; FT-SEC (AT-1..20) | Security |
| RK-11 | Fabrication/placeholder/dummy leaks in (scope/quality) | M | H | AGENT §14 + pre-commit semantic scan + hostile review (IMPL-802) | All |
| RK-12 | SEC-1 keystore not remediated before release | M | H | IMPL-804 remediation + secret scan; AGENT §7 | Security |
| RK-13 | Perf targets unmet (queries/aggregation) | M | M | indexes/CF counters; FINAL-PERFORMANCE measure (IMPL-703/803) | Eng |
| RK-14 | Missing brand/logo asset → blocked a11y/UI polish | M | M | IMPL-704 BLOCKED — MISSING ASSET; report; don't invent | Product/Design |
| RK-15 | Test contract not fully executed / gates weakened | M | H | Phase-8 IMPL-801; no disabled gates (AGENT §14.19) | QA |

Legend: L/I = Low/Med/High. Residual risks accepted only with documented owner decision and monitoring.
