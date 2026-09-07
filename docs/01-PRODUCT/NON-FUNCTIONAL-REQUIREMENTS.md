# Non-Functional Requirements (current + proposed)

Status: VERIFIED unless labelled PROPOSED. Commit `2dd2fe4`.

## Current NFRs
| NFR-ID | Category | Requirement | Status |
|---|---|---|---|
| NFR-01 | Offline | 100 % local operation; no network dependency | VERIFIED |
| NFR-02 | Persistence | Data survives restarts (Hive on device) | VERIFIED |
| NFR-03 | Performance | `TripLabour` ops avoid full scans: composite-key prefix reads + `putAll` bulk writes | VERIFIED (see `05-PERFORMANCE/`) |
| NFR-04 | Startup UX | Splash + skeleton loading on dashboard | VERIFIED |
| NFR-05 | Reliability | Backup restore rolls back on failure; pre-restore snapshot | VERIFIED |
| NFR-06 | Security | No secrets required at runtime (client has no secret keys) | VERIFIED (no runtime secret) |
| NFR-07 | Single-user integrity | Local data-integrity rules (orphan prevention, cascade delete) | VERIFIED/partial |
| NFR-08 | Android min | `minSdk 24` | VERIFIED (`build.gradle.kts`) |
| NFR-09 | Data safety | Exportable backup for device migration | VERIFIED |
| NFR-10 | Accessibility | Screen-reader/contrast/TalkBack readiness | MISSING/PARTIAL — see accessibility audit |
| NFR-11 | Localization | i18n (plural, RTL, regional formats) | MISSING — hardcoded en-US |
| NFR-12 | Large screens/tablet | Responsive layout | MISSING/PARTIAL — fixed 360×690 design space |
| NFR-13 | Telemetry | Crash reporting/analytics/performance monitoring | MISSING (by design offline) |
| NFR-14 | CI/CD | Automated pipeline / quality gates | MISSING (no `.github`) — see `07-OPERATIONS/` |
| NFR-15 | Automated test coverage | Test suite present (27 files) | VERIFIED presence; coverage partial |
| NFR-16 | Release signing | Release keystore config via `key.properties` (not committed) | VERIFIED intent; but see keystore finding |

## Proposed NFRs (native) — PROPOSED
| NFR-ID | Category | Requirement |
|---|---|---|
| PNFR-01 | Security | Backend-authoritative authz; no client-trusted role checks |
| PNFR-02 | Performance | Cold start ≤ 2 s target; baseline profiles; recomposition discipline; macrobenchmarks |
| PNFR-03 | Availability | Offline-first with server reconcile; conflict policy defined |
| PNFR-04 | Observability | Crashlytics + structured logs (no secrets); non-PII analytics only if justified |
| PNFR-05 | Accessibility | Compose semantics, TalkBack, contrast AA, large-font, reduced motion |
| PNFR-06 | Backup/recovery | Server backup + retention + DR runbooks |
| PNFR-07 | CI/CD | Pipeline with static analysis, unit/integration/UI tests, security scan, release gate |

## Note on measurement
No runtime performance/quality telemetry exists in the current offline app; any numeric NFR target above is a **target**, not a measured current value. `UNVERIFIED`.
