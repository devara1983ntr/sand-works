# FINAL TRACEABILITY MATRIX — V1 (Phase 0.75)

Chain: Requirement → Feature → Screen → Action → Use Case → Database → Backend → Authorization → Error → Notification → Test. Representative rows for every V1 capability cluster; the full set is enforced because each artefact lists the same IDs. A V1 requirement with a missing link is a defect (IMPLEMENTATION-CONTRACT).

| Requirement (source) | Feature | Screen | Action | UC | DB (collection) | Backend op | Authz | Error | Notification | Test |
|---|---|---|---|---|---|---|---|---|---|---|
| Owner auth (PRD proposed; SEC) | FV-1 | N-01/03/07/08 | Login | UC-auth | users | Auth+B-01 | OWNER claim | Auth/Offline/Disabled/Expired | none/security | FT-AUTH/FT-A1 |
| Session model D-5 (FR-03) | FV-10 | N-24 | create session | UC-2 | workSessions + meta | B-02 | OWNER | Conflict/uniqueness | — | FT-B1/FT-RULES |
| Trip + numbering (BR-2/R-10) | FV-11 | N-25 | add/next-trip | UC-3 | trips | B-03 | OWNER | Conflict/duplicate/offline | — | FT-B2/FT-CONC |
| Attendance integrity (R-20..23) | FV-12 | N-23 | record/correct | UC-5 | attendance+history | B-04 | OWNER | Validation/partial/conflict | — | FT-B3/FT-AUD |
| Dashboard (FR-02/UC-1) | FV-13 | N-20 | view/search | UC-1/4 | workSessions+trips | reads | OWNER | Empty/Error/Offline | — | FT-B4/FT-UI |
| History/search | FV-14 | N-27/history | browse/search | UC-7 | workSessions+trips | reads | OWNER | Empty/offline | — | FT-B5 |
| Delete soft (R-30/31) | FV-15 | N-20/22/23 | delete | UC-6 | trip+attendance | B-06 | OWNER | Conflict | — | FT-B6/FT-AUD |
| Close session (R-40) | FV-16 | N-22 | close | UC | workSessions | B-05 | OWNER | already-closed | — | FT-B7/FT-CONC |
| Labourer catalogue | FV-20 | N-29 | CRUD | UC-crew | labourers | B-07 | OWNER | Validation | — | FT-C1 |
| Driver/vehicle catalogue | FV-21/22 | N-31/30 | CRUD | UC | drivers/vehicles | B-07 | OWNER | Validation | — | FT-C2 |
| Analytics (FR-14) | FV-30 | N-27 | view | UC-8 | counters/trips | reads/CF | OWNER | Empty/Error | — | FT-D1 |
| CSV export (S1) | FV-31 | N-27/28 | export | UC | exports | B-13 | OWNER | Storage/Timeout | completion | FT-D2 |
| Backup/restore (FR-15/16) | FV-40 | N-38 | backup/restore | UC-9 | backups + Storage | B-11/12 | OWNER | Storage/verify-fail/conflict | S4 complete/fail | FT-E1/FT-E2 |
| Profile | FV-50 | N-40 | edit self | UC | users | B-09 | self | Validation/photo | — | FT-F1 |
| Account/security | FV-51 | N-39 | password/delete-data | UC | Auth+anonymize | B-10 | self/OWNER | re-auth/guarded | security alert | FT-F2/FT-SEC |
| Settings | FV-52 | N-37 | configure | UC | settings | B-08 | OWNER | Validation/rev | — | FT-F3 |
| Owner notif centre (S4) | FV-60 | N-41 | view/open | UC | notifications | B-14 | OWNER/self | NotFound-fallback | deep-link | FT-G1 |
| Audit viewer (S5) | FV-70 | N-36 | view | UC | auditLogs | (read) | OWNER | Empty/offline | — | FT-H1/FT-AUD |
| Offline/sync | (cross) | all write | op | — | Room outbox→Firestore | B-* | OWNER | OFFLINE/Conflict | — | FT-OFFLINE/FT-CONC |
| Security rules | (cross) | all | all | — | all | Rules+CF | per FINAL-RBAC | Forbidden | — | FT-RULES/FT-SEC |
| A11y/responsive/perf/loc/rel | (cross) | all | — | — | — | — | — | — | — | FT-A11Y/RESP/PERF/LOC/REL |

## Missing-link rule
Implementation agents must fill the requirement→…→test chain for anything they build and flag any row they cannot fully link rather than proceeding with a guess (IMPLEMENTATION-CONTRACT §ambiguity). Deferred (D-1/D-2/D-6) capabilities are intentionally absent from V1 rows and marked DEFERRED.
