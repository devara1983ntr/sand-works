# Quality Risks

Status: VERIFIED current; PROPOSED mitigations. Commit `2dd2fe4`.

| ID | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| QR-1 | Signing-key exposure from committed keystore backup | MED (already present) | HIGH | Rotate; purge; secret-scan CI |
| QR-2 | Data loss via silent partial-write in full-trip save (not transactional) | LOW-MED | HIGH | Wrap saves in batch/transaction; verify counts |
| QR-3 | No schema migration → future model change breaks Hive | MED (long-term) | HIGH | Version & migrate; add migration tests |
| QR-4 | Unauthenticated single-user assumption breaks if product opened up | HIGH (if scope changes) | HIGH | Native adds auth/RBAC from the start |
| QR-5 | Backup plaintext PII + no integrity | MED | MED | Encrypt/authenticate |
| QR-6 | Runtime execution/test pass not reproduced in this audit | MED | MED | Re-run suite in a Flutter-enabled CI before claiming readiness |
| QR-7 | Dead/inert code and misleading swipe affordance erode UX confidence | MED | LOW-MED | Clean up (safe, cheap) |
| QR-8 | Duplicate definitions risk merge conflicts/reintroduction | LOW-MED | MED | Single shared models; automated checks |
| QR-9 | No crash/telemetry (offline) → invisible issues | MED | MED | Native Crashlytics; manual QA now |
| QR-10 | Pre-existing docs may over-claim production readiness | MED | MED | Reconcile docs with verification (this suite) |

## Overall
The product is functionally coherent for a single-user offline tool but carries one critical security/ops issue (keystore), several data-integrity/migration risks, and a testing-execution verification gap.

## Verification status
Risks VERIFIED statically. Probabilities are qualitative judgments (not measured).
