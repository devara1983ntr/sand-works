# Native Android Implementation Roadmap (Phase 0 → implementation)

Status: PROPOSED. Phase 0 architecture is the gate into implementation. **Do not implement until the Phase-0/0.5/0.75 gates pass and open decisions are resolved.** Production release blocked until audit SEC-1 (keystore) remediated. Read `FINAL-READINESS-REPORT.md` and `IMPLEMENTATION-CONTRACT.md` before starting; honour `PRODUCT-FREEZE.md` (S-V1 scope).

## Phase 0 (done)
Reconciliation + architecture + Firebase/RBAC/rules design + offline/sync + notifications + security + testing + CI/CD + ADRs. Output: `PRODUCT-RECONCILIATION.md` and the full `08-NATIVE-ANDROID/` set + `ADR/`.

## Phase 0.5 — Product coverage & gap audit (done, spec-only)
Specification-only coverage sweep: screen inventory + per-screen specs + state/error/loading/empty/offline matrices, conditional-logic, business-rule×role, user-action-reaction, navigation+Back+bottom-nav audits, journeys, workflows, gestures, forms+server validation, responsive (M3 adaptive), accessibility, wireframes, system/E2E flows, edge cases, concurrency, data-consistency, analytics, audit-log, test-coverage, backend-op register (BO-1..16), database state machines, Firestore authz matrix, Firebase coverage, notification event register. **Master aggregation artifact: `PRODUCT-GAP-REGISTER.md` (G-001..; CRITICAL 7 / HIGH 13 / MEDIUM 19 / LOW+Decision 6).** No code, no fake data, no invented functionality.

## Phase 0.75 — Final design closure gate (done, spec-only)
Froze recommended scope **S-V1 (owner-only)** and produced the implementation contract: `IMPLEMENTATION-CONTRACT.md`, `PRODUCT-FREEZE.md`, and the `FINAL-*`/integrity/security set listed in `DOCUMENTATION-INDEX.md`. No code/Firebase resources.

**Phase-0/0.5/0.75 exit criteria** (do not start implementation until):
- Open decisions D-1..D-8 answered or explicitly flagged.
- Critical gaps G-001..G-007 and high gaps G-101.. resolved/planned per PRODUCT-GAP-REGISTER.
- Version matrix verified (DEPENDENCY-POLICY) on a Flutter-independent machine.
- Keystore SEC-1 remediated (rotate + purge).
- No DECISION REQUIRED in the docs blocks implementation.

## Phase 1 — Foundation & offline core (no backend yet)
Project skeleton (Kotlin/Compose/M3 stable), Hilt, design system, Navigation graphs (auth placeholder gated), Clean Architecture packages, Room outbox+cache, DataStore, local domain entities + repository interfaces + unit/domain tests. Port owner day-entry/trip/attendance behaviour as local-first (KEEP behavioural scope).

## Phase 2 — Authentication & roles
Firebase Auth (email/password), provisioning Cloud Function for owner/admin (no hardcoded creds), role model, session/forbidden handling, role-driven nav.

## Phase 3 — Backend data & security
Firestore schema + Security Rules + **Emulator rule tests** (mandatory) + App Check; Firestore datasources + mappers; offline/sync reconcile + WorkManager; CF for privileged ops + audit.

## Phase 4 — Role journeys & notifications (scope-dependent D-1/D-6)
Driver/labourer (if enabled): role homes, assigned-trip reads, CF-gated status/attendance confirm. Notifications (FCM + CF + token lifecycle) if in v1.

## Phase 5 — Reports, backup/DR, observability
Owner reports; encrypted/signed backup/export + cloud backup; Crashlytics + monitoring + alerting; audit tooling.

## Phase 6 — Release hardening
Accessibility & localization; performance (macrobenchmark/baseline profiles); release gate (PRE-RELEASE.md); staged rollout; post-release monitoring; Play App Signing.

## Cross-cutting
Tests every phase (unit/ViewModel/Compose/Emulator-rules/RBAC/offline/sync/a11y). Keep docs truthful (AGENT.md). No production mocks/placeholders.

## Migration note
Optional importer for `.labourbackup`/Hive only if D-3 confirms production data (MIGRATION-STRATEGY.md).

## Verification
Phase 0 spec complete; implementation not started (NOT READY until gate passes).
