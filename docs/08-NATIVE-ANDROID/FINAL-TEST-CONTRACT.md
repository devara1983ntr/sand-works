# FINAL TEST CONTRACT — V1 (Phase 0.75)

Every requirement maps to a test. Categories below; a requirement with no test is a defect. Reference ANDROID-TESTING-ARCHITECTURE + TEST-COVERAGE-MATRIX reconciled to S-V1.

## Test suites & ownership
| Suite | Scope | Key coverage |
|---|---|---|
| Unit | usecases/domain/validation/numbering/state machine/date-session | R-rules, D-5 boundary, forms, idempotency keys |
| Repository | Room outbox mapping, retry, offline replay, concurrency helpers | OFFLINE/queue/dep-order |
| ViewModel | UDF, UiState, action→reaction, errors, session-expiry | FINAL-UI-STATE / ACTION-REACTION |
| Compose UI | every screen Happy/Empty/Loading/Error/Offline/Forbidden/Submitting/Conflict + nav/back | FINAL-SCREEN-STATE per screen |
| Navigation | route entry/exit/args/authz/back/deep-link/notification/session | FINAL-NAVIGATION |
| Firebase Emulator | rules + functions local | FT-RULES/FT-CF |
| Security Rules | Firestore+Storage every cell, escalation, ownership, delete, immutable fields | FT-RULES/FT-SEC |
| Authentication | login/recovery/session/disabled/role claim | FT-AUTH |
| RBAC | OWNER vs deferred roles, UI==Rules | FT-RBAC |
| Offline/Sync | offline write→reconnect→sync no-fake-success, order | FT-OFFLINE |
| Conflict | rev, double-submit, unique race, close race, restore-overwrite | FT-CONC |
| Notifications | S4 events, deep link, read state, retry | FT-G |
| Accessibility | TalkBack semantics, target, contrast, scale, focus, keyboard, live region, reduced motion | FT-A11Y |
| Localization | string resources, plurals, RTL-safe, locales | FT-LOC |
| Performance | macrobenchmark/startup/frame/perf scenarios | FT-PERF |
| End-to-End | FE1..FE8 full integration w/ DB+audit | FT-E2E |
| Release | PRE-RELEASE gate, signed build, secret hygiene (SEC-1), baseline profile | FT-REL |

## Traceability anchors (each suite references R/FV/BO/B-IDs)
- Features FV-* → screens → actions (FINAL-ACTION-REACTION) → tests.
- Business rules R-* → rule unit test + emulator rule test.
- Backend ops B-* → CF test (emulator) for validation/txn/idempotency/audit/notification.
- Security attacks AT-* (SECURITY-ATTACK-REVIEW) → FT-SEC.
- Edge cases E-* (EDGE-CASE-AUDIT) → suite mapping.
- States/UI FINAL-SCREEN-STATE → Compose UI test each.
- Indexes (FINAL-QUERY-RULE-MATRIX) → emulator query tests.
- Audit/Audit-integrity → FT-AUD (truthful row, forge reject, correction history).
- Attendance integrity → FT-AUD/FT-B3.

## No-empty rule
A (feature × role × scenario) with no test is flagged; gated V2 items list their future test explicitly (not silent). FT-REL runs on a clean machine; mocks only in isolated tests; no fake production data anywhere.

## Coverage gate (definition of done for V1)
100% of R-rules have unit+rule tests; every V1 screen has a state test; every B-op has CF test; every AT has FT-SEC; every FE flow is an E2E test; a11y + responsive + performance + release gates green (or documented owner-approved exception). 
