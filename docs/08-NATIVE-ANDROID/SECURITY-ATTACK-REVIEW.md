# SECURITY ATTACK REVIEW — V1 (Phase 0.75)

Hostile attacker model against the V1 (single-owner, Firestore + CF + Storage + Auth + App Check) design. Columns: Attack · Likelihood · Impact · Control · Backend enforcement · Test. V1 single-owner makes several trivial, but the controls are real and V2-safe.

| # | Attack | Likelihood | Impact | Control | Backend enforcement | Test |
|---|---|---|---|---|---|---|
| AT-1 | Driver becomes ADMIN / role escalation | Low (V1 single role) | Critical | No client role write; role stored owner-set by CF; rules deny `users.role`/status client write | Rules deny; CF only | FT-SEC role escalation |
| AT-2 | Labourer reads another worker's data | Low (no labourer login V1) | High | Owner-only reads; no cross-user read paths | rules scoped by org+owner | FT-RULES |
| AT-3 | User modifies own role/ownerId/orgId | Low | Critical | immutable server fields; client write denied | rules deny + immutable | FT-SEC |
| AT-4 | Client writes auditLogs / deletes audit | Low | High | audit CF-only; rules deny client write/delete to auditLogs | rules deny; append-mostly | FT-AUD |
| AT-5 | Modify completed/closed work | Medium | High | session-state guard + rev + audit; corrections need reason | CF transition guard | FT-CONC/FT-AUD |
| AT-6 | Bypass assignment/sequence (forged tripNumber) | Medium | High | numbers CF-authoritative in txn | CF transaction | FT-CONC |
| AT-7 | Access another organization's data | Low (V1 single org) | Critical | orgId on every doc; queries+rules require org match | rules equality | FT-RULES |
| AT-8 | Deleted/disabled user retains access | Medium | High | status check server-side each op (rules read users.status/claim); token revoked; local cache cleared on disable | rules + claims | FT-AUTH/FT-RULES |
| AT-9 | Offline operations replay incorrectly / duplicate | Medium | Medium | idempotency opId dedupe; ordered outbox replay | CF dedupe | FT-CONC/FT-OFFLINE |
| AT-10 | Notification deep link bypasses authorization | Medium | Medium | deep link target re-validates auth+owner+existence; NotFound/Forbidden fallback | rules on target | FT-NAV/FT-RULES |
| AT-11 | Storage files accessed directly / public | Medium | High | Storage rules owner-only/self; never public read; signed URLs with expiry for exports | Storage rules + signed URL | FT-RULES-storage |
| AT-12 | Client forges timestamps | Medium | Medium | server timestamps (FieldValue); rules reject literal createdAt/updatedAt | rules | FT-SEC |
| AT-13 | Duplicate mutations create duplicates | Medium | Medium | uniqueness (session,trip#) in txn + opId dedupe | CF | FT-CONC |
| AT-14 | Malicious/unauth client (app check bypass) | Medium | High | App Check (Play integrity) required on Firestore/Storage/Functions | App Check | FT-SEC |
| AT-15 | Backup tampering / plaintext PII | Medium | High | encrypted+signed backup; validation/rollback on restore; PII at rest handled | signing+checksum | FT-E1 |
| AT-16 | Mass query / cost abuse (owner's own token) | Medium | Medium | indexes only, no unbounded scans; rate limits; Cloud monitoring alerts | rules + monitoring | FT-PERF/ops |
| AT-17 | Password reset enumeration / brute force | Medium | Medium | Auth rate limits; generic reset message; re-auth for destructive | Auth | FT-A2/FT-SEC |
| AT-18 | Log injection / secrets in logs | Low | Medium | never log secrets/PII; typed errors; audit excludes secrets | CF | FT-AUD/obs |
| AT-19 | Session fixation / expired token reuse | Low | Medium | short token lifecycle; session-expiry re-auth preserving outbox; clear local cache | Auth | FT-AUTH |
| AT-20 | Client trusts UI-role to skip backend | High (attempt) | — | Rule-of-three: rules+CF always enforce regardless of UI | rules/CF | FT-RULES |

## Summary
Critical controls all enforced **backend-side** (rules + CF + App Check), not in Compose. V1 single-owner limits real attackers to the device owner + network adversary; all of the above remain valid and tested so V2 (roles) inherits a sound model. Residual LOW/medium items are accepted with documented monitoring.
