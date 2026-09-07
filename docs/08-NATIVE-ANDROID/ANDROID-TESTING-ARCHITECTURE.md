# Android Testing Architecture

Status: PROPOSED (Phase 0). Defines a layered test strategy and mandatory Firebase-rules automated tests. Security-critical rules MUST be automated against the Emulator.

## 1. Test pyramid
```
E2E (small)
Compose UI / Navigation
ViewModel
Repository / Domain (unit)
Unit (pure)
FIREBASE EMULATOR rules tests (cross-cutting, MUST be automated)
```
| Layer | Scope | Tech |
|---|---|---|
| Unit | pure domain, usecases, mappers | JUnit |
| Domain/Repository | business rules w/ fake repos | JUnit + fake/mock (test-only) |
| ViewModel | UiState transitions, events | coroutines-test, fakes |
| Compose UI | per-screen flows & states | Compose UI test + Robolectric/emulator |
| Navigation | route/back/deeplink | Compose Nav test |
| Firebase Emulator | Security Rules (auth), storage rules, CF, sync | Emulator + rules-unit-tests |
| E2E | critical journeys | instrumented on emulator (dev data only) |

## 2. Mandatory suites
- **Owner tests**: CRUD work/trip, record attendance, manage drivers/labourers, view reports.
- **Driver tests**: login, view own assigned trips only, (permitted) status confirm via CF.
- **Labourer tests**: login, view own attendance only, cannot modify another's (DENY).
- **Unauthorized access**: role trying forbidden action = Forbidden state; unauthenticated = redirect.
- **Authentication**: sign-in/out, disabled account, expired session, account recovery.
- **Firestore rule tests** (emulator): every (actor × verb × resource) from RBAC matrix → ALLOW/DENY expected.
- **Storage rule tests**: upload/download authorization, content-type/size guards.
- **Notification tests**: token reg, role-scoped delivery, deep link authz.
- **Offline tests**: cached reads, offline write queue, retry/backoff, no fake-success.
- **Sync tests**: success/conflict/rejection reconciliation, no silent overwrite.
- **Accessibility tests**: semantics, contrast (design tokens), TalkBack smoke on critical flows.
- **Error-state tests**: every screen's Error/Empty/Offline/Unauthorized/Forbidden states + retry.

## 3. Fixture & emulator policy
- Never use production Firebase data as fixtures.
- Never run destructive development tests against production.
- Use Firebase Emulator Suite (Auth+Firestore+Functions+Storage) for local dev & CI.
- Mocks only inside tests; **no production mocks/fakes**.

## 4. Test data
Domain/fixtures from audited entities; a `testdata` module generating org/drivers/labourers/trips for owner, driver, labourer rule fixtures.

## 5. CI integration
Runs in pipeline (see `CI-CD-ARCHITECTURE.md`): unit→rules(emulator)→Compose→E2E on emulator with dev project.

## 6. Verification
PROPOSED. Existing Flutter test suite (27 files) is reference for behavioural coverage; native re-implements coverage per this doc.
