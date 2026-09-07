# IMPLEMENTATION CONTRACT (Phase 0.75)

The primary contract consumed by the future coding agent. **Do not implement until the readiness gate (§Readiness) passes** (owner confirms scope S-V1 + D-3/D-4). This contract makes the documents binding and references the full authoritative set.

## 1. Product & scope
- Product: offline-first single-owner labour/trip manager (S-V1). **PRODUCT-FREEZE.md** is binding: build MUST/selected SHOULD only; NEVER build OUT OF SCOPE or DEFERRED.
- Driver/labourer self-service, ADMIN, driver status workflow, cross-role notifications, payroll, legacy import are NOT in V1.

## 2. Source of truth & classification
Use, in order: repository code > runtime (UNVERIFIED, no SDK) > tests > PRD/PRD2 > Phase-0 docs/ADR > Phase-0.5 docs > **this FINAL set** (Phase 0.75). Classify every claim VERIFIED/INFERRED/UNVERIFIED/PROPOSED. Never treat an assumption as fact.

## 3. Mandatory referenced contracts (read in this order)
1. PRODUCT-FREEZE, FINAL-DECISION-REGISTER, FINAL-FEATURE-CATALOG, FINAL-SCREEN-CATALOG.
2. FINAL-NAVIGATION (+Back), FINAL-SCREEN-STATE-MATRIX, FINAL-UI-STATE-CONTRACT, FINAL-ACTION-REACTION-MATRIX.
3. FINAL-CONDITIONAL-LOGIC, FINAL-BUSINESS-RULES, FINAL-WORK-STATE-MACHINE, FINAL-FORMS, FINAL-GESTURES, FINAL-ERROR-CONTRACT.
4. FINAL-DATABASE-SCHEMA, FINAL-QUERY-RULE-MATRIX, CONCURRENCY-SPECIFICATION, ATTENDANCE-INTEGRITY, AUDIT-LOG-INTEGRITY, SERVER-AUTHORITY-MATRIX, FINAL-OFFLINE-SYNC.
5. FINAL-BACKEND-CONTRACT, FINAL-FIREBASE-SECURITY-MODEL, FINAL-RBAC-MATRIX, SECURITY-ATTACK-REVIEW.
6. FINAL-ACCESSIBILITY, FINAL-RESPONSIVE, FINAL-PERFORMANCE-CONTRACT, FINAL-WIREFRAMES, FINAL-E2E-FLOWS, FINAL-TRACEABILITY-MATRIX, FINAL-TEST-CONTRACT.
7. Design system: ANDROID-DESIGN-SYSTEM, ANDROID-ARCHITECTURE, DOMAIN-MODEL, ADR/001..015, IMPLEMENTATION-ROADMAP, DEPENDENCY-POLICY, CI-CD-ARCHITECTURE.

## 4. Non-negotiable implementation rules
- **Never invent product behaviour.** Follow the FINAL docs.
- **Ambiguity:** if the spec is ambiguous for a critical path, STOP and report it. Do not guess.
- **No fake production data.** No mocks posing as real. No hardcoded accounts.
- **No fake authentication.** Provisioning via B-01 (CF) only; owner identity is a parameter, never hardcoded.
- **Never bypass Firebase Security Rules** (Firestore/Storage). Rules + App Check are enforcement.
- **Never treat client-side RBAC as security.** UI hiding ≠ authorization; rules/CF are the gate.
- **No undocumented dependencies.** Follow DEPENDENCY-POLICY; re-verify versions.
- **Never silently change business rules.** A change is an owner-approved product change recorded in docs + PRODUCT-GAP-REGISTER.
- **Add tests for implemented behaviour** (FINAL-TEST-CONTRACT).
- **Update documentation when approved behaviour changes** (truthful classification; no TODO/TBD to hide incomplete work).

## 5. Security posture (from FINAL-FIREBASE-SECURITY-MODEL / SECURITY-ATTACK-REVIEW)
- Server-authoritative values (SERVER-AUTHORITY-MATRIX); CF for privileged ops; audit CF-only; encrypted+signed backup; App Check; owner+org scoping; disable/delete with guards; rate limiting/monitoring.
- SEC-1 keystore remediation is a release blocker (rotate/purge, AGENT §7).

## 6. Offline & integrity
- No fake success; queued/pending UI; idempotent replay; conflict → owner prompt; attendance append-immutable; optimistic rev; uniqueness/numbering in CF transactions.

## 7. Quality gates before V1 ship
- Phase-0/0.5/0.75 decisions confirmed; D-3/D-4 resolved; SEC-1 remediated; every FINAL-TEST suite green (incl. emulator rules + a11y + responsive + performance); release gate (PRE-RELEASE.md) passes on clean machine; docs updated.

## 8. Readiness (reference FINAL-READINESS-REPORT)
**NOT READY until owner confirms scope S-V1 and resolves D-3/D-4.** Once confirmed, this contract is fully actionable with no remaining product/architecture guess-work for the V1 OWNER scope.
