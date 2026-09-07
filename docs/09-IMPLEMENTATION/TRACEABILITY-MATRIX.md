# TRACEABILITY MATRIX (implementation layer)

Chain: Requirement → Feature → Screen → Use Case → State → Domain Rule → Data Model → Repository → Backend Op → Security Rule → Test → **Implementation Task**.
Primary source for requirement-level linkage: `08-NATIVE-ANDROID/FINAL-TRACEABILITY-MATRIX.md` + `FINAL-FEATURE-CATALOG.md`. This file adds the implementation-task mapping so there are **zero unexplained required features** and **zero orphan tasks**.

## Feature → Task mapping (V1 scope)
| Feature (FV) | Screen | Backend | Primary task(s) |
|---|---|---|---|
| FV-1..4 Auth/lifecycle | N-01,03,04,05,06,07,08 | Auth,B-01 | IMPL-302,303,401 |
| FV-10 Open/New session | N-24,N-22 | B-02 | IMPL-404 |
| FV-11 Record trip | N-25 | B-03 | IMPL-405 |
| FV-12 Attendance | N-23 | B-04 | IMPL-406 |
| FV-13 Dashboard | N-20 | reads | IMPL-403 |
| FV-14 History/search | N-27/history | reads | IMPL-408 |
| FV-15 Delete soft | N-20/22/23 | B-06 | IMPL-407 |
| FV-16 Close session | N-22 | B-05 | IMPL-404 |
| FV-20 Labourers | N-29 | B-07 | IMPL-501 |
| FV-21 Drivers | N-31 | B-07 | IMPL-502 |
| FV-22 Vehicles (S2 opt) | N-30 | B-07 | IMPL-503 |
| FV-30 Analytics | N-27 | reads/CF | IMPL-504 |
| FV-31 CSV (S1 opt) | N-28 | B-13 | IMPL-505 |
| FV-40 Backup/restore | N-38 | B-11/12 | IMPL-506 |
| FV-41 Cloud backup (opt) | N-38 | B-11/12 | IMPL-507 |
| FV-50 Profile | N-40 | B-09 | IMPL-601 |
| FV-51 Account/security | N-39 | B-10/Auth | IMPL-602 |
| FV-52 Settings | N-37 | B-08 | IMPL-603 |
| FV-53 Help (S3 opt) | N-42 | — | IMPL-605 |
| FV-60 Notif (S4 opt) | N-41 | B-14 | IMPL-604 |
| FV-70 Audit viewer (S5 opt) | N-36 | read | IMPL-606 |

## Cross-cutting requirement → task mapping
| Requirement domain | Task(s) |
|---|---|
| Business rules R-01..R-90 (domain) | IMPL-109 (+ rule tests); enforcement in B-ops IMPL-305 |
| DB schema / collections / integrity | IMPL-108,110,201,406(attendance integrity) |
| Queries rule-compatible | IMPL-304,201,703 |
| Concurrency (idempotency/rev/txn/conflict) | IMPL-203,204,305,406/405 conflicts |
| Offline/sync no-fake-success | IMPL-202,203 (+ every screen) |
| Audit integrity (server-generated) | IMPL-306 (+305), 606 viewer |
| Server-authority matrix | IMPL-306,304 |
| Security attacks AT-1..20 | IMPL-304,305,306,307,802 |
| RBAC/org scoping | IMPL-307,304 |
| Notifications (owner) | IMPL-604 (B-14 IMPL-305) |
| Errors/loading/empty/offline states | every screen task via sub-checklist; foundation IMPL-105 |
| Accessibility | IMPL-701 + every screen |
| Responsive M3 adaptive | IMPL-702 |
| Performance | IMPL-703 |
| Assets/brand | IMPL-704 |
| Localization | IMPL-705 |
| Test contract (17 suites) | IMPL-801 |
| Release/SEC-1/CI-CD | IMPL-804,805 |

## Orphan audit
- Required features (V1 24): all mapped above → 0 unexplained.
- Orphan tasks: every task lists Downstream/upstream and maps to a requirement or infrastructure — audit in IMPL-805 (§27) confirms 0.

## Agent safety note
If the executing agent implements behaviour not traceable to a requirement here or in FINAL-TRACEABILITY, it must STOP and report rather than add unexplained functionality (R5, AGENT §14).
