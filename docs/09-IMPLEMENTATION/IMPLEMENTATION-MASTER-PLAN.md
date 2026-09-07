# IMPLEMENTATION MASTER PLAN

Status: PLANNING. Programme-level view. The plan converts the frozen/approved specification into a dependency-aware implementation programme. Implementation is currently BLOCKED (see `DECISION-REGISTER.md`).

## 1. Programme objective
Deliver the V1 **OWNER** native Android app (scope S-V1) exactly as specified, with real, truthful, secure, tested implementation, and **nothing** fabricated, placeholder, silently omitted, or outside approved scope. Every task is traceable to the authoritative spec; no agent makes product/architecture decisions.

## 2. Governing documents
- Binding rules: `docs/03-ENGINEERING/AGENT.md` (esp. §14 Zero-Placeholder / §14.29 immutability), `08-NATIVE-ANDROID/IMPLEMENTATION-CONTRACT.md`.
- Scope: `08-NATIVE-ANDROID/PRODUCT-FREEZE.md`.
- Final decisions/blockers: `08-NATIVE-ANDROID/FINAL-DECISION-REGISTER.md`, and this dir's `DECISION-REGISTER.md`.

## 3. Phase model (derived from the actual dependency graph, not predetermined)
| Phase | Name | Purpose | Depends on | Exit gate |
|---|---|---|---|---|
| 0 | Readiness gate | Confirm scope S-V1, D-3/D-4, env/credentials | — | Gate-0: IMPLEMENTATION STATUS READY |
| 1 | Foundations | Gradle/variants/DI/nav/theme/logging/domain/local | Gate-0 (identity D-4) | Gate-1 |
| 2 | Data & offline layer | Room/outbox/repositories/sync/conflict/concurrency | 1 | Gate-2 |
| 3 | Auth & security foundation | Firebase Auth, App Check, Rules, CF (B-01..14), audit, org scoping | 1,2 | Gate-3 (needs real Firebase env) |
| 4 | Owner core workflow | Dashboard/session/trip/attendance/delete/history | 2,3 | Gate-4 |
| 5 | Catalogues + reporting/backup | N-29/30/31, N-27/28 analytics+export, N-38 backup/cloud | 4 | Gate-5 |
| 6 | Account/settings/secondary | N-40/39/37 profile+security+settings; N-41/42/36 optional (S4/S3/S5) | 4 | Gate-6 |
| 7 | Cross-cutting completion | Accessibility, responsive, performance, assets, localization | all | Gate-7 |
| 8 | Verification & release | Full test contract, security/rules, emulator, perf, release gate | all | Gate-8 (release) |

Parallelisation and critical path are in `DEPENDENCY-MATRIX.md`.

## 4. Scope boundaries (hard)
IN (V1 S-V1): the 24 V1 features (FV-1..FV-70 V1 set) across 25 screens + 14 backend ops + 27 business rules + 20 attack controls + 17 test suites.
NOT IN (do not implement): DEFERRED FV-80..86 (driver/labourer self-service D-1, ADMIN D-2, driver workflow D-6, cross-role notif, payroll, legacy import D-3) and OUT OF SCOPE FV-90 (web/desktop/biometric-PII/ML/ad).

## 5. Execution philosophy
Small, independently verifiable tasks; strict dependency order; no task started until prerequisites complete; no phase COMPLETE until its gate passes (code compiling is not completion); evidence-based tracking; no fabrication under any circumstance (AGENT §14).

## 6. Acceptance definition
The programme is complete when every READY-scope task is COMPLETE with evidence, every phase gate passes, IMPLEMENTATION CONTROL final audit is clean, and the release gate (`08-NATIVE-ANDROID/FINAL-TEST-CONTRACT.md`, `docs/06-QUALITY/PRE-RELEASE.md`) passes on a clean machine.
