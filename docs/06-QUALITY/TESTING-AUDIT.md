# Testing Audit (current)

Status: VERIFIED (static scope); execution UNVERIFIED. Commit `2dd2fe4`.

## Findings
| ID | Sev | Finding | Evidence |
|---|---|---|---|
| T-1 | MED | Execution of the suite not independently reproduced (no SDK in env); pass state UNVERIFIED | environment |
| T-2 | MED | `restore_logic_test` does not unit-test the private `_parseAndValidateBackup` function directly; tests re-implement rules | restore_logic_test.dart header comment |
| T-3 | MED | No golden/screenshot tests; regression relies on widget tests | repo |
| T-4 | LOW | No coverage report in repo root; coverage only mentioned in prior docs | docs/production_readiness/coverage_report.md (not verified) |
| T-5 | MED | No accessibility tests, no security tests, no RTL/locale tests | repo |
| T-6 | LOW | Performance tests exist but are microbenchmarks; no macrobenchmark/startup/jank test | test/performance |
| T-7 | MED | No error/offline/retry-path UI tests (though some error states are rendered) | test/widget |
| T-8 | INFO | Positive: unit/widget/integration split is sensible; mocks isolated in test helpers | structure |

## Test hygiene (VERIFIED)
- Mocks only inside tests. No placeholder/dummy production backend. Good.

## Gaps to close for native
Per `TESTING.md`. Emphasise: critical journey coverage (create trip → attendance → history), error/offline, RBAC/security, accessibility, and macrobenchmark.

## Verification status
Static audit VERIFIED. Any "all tests pass" claim UNVERIFIED in this environment.
