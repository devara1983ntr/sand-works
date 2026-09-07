# Backend / API Contract

Status: PROPOSED (Phase 0). Even where Firestore is accessed directly, each operation is specified as a domain contract. Structure per op: operation, actor, authorization, inputs, validation, result, error, side effects, audit event, notification, retry.

## 1. Contract template
| Field | Meaning |
|---|---|
| Operation | canonical name |
| Actor | role(s) that may call |
| Authorization | server enforcement (rules/CF), not client claim |
| Inputs | typed request |
| Validation | domain/business rule checks |
| Result | success payload/state |
| Error | typed error (auth/forbidden/offline/conflict/validation) |
| Side effects | secondary writes/CF |
| Audit event | audit log entry |
| Notification | FCM (type/audience) |
| Retry | idempotency + backoff |

## 2. Sample contract table
| Operation | Actor | Authorization | Notes |
|---|---|---|---|
| Sign in | any | Auth | session |
| Get own profile | self | rules self | |
| Update own profile fields | self | self limited fields | no role/email change |
| List org users | OWNER/ADMIN | rules org+role | |
| Manage user/role/status | OWNER | **CF only** | atomic + audit; no client direct |
| Create work session | OWNER/ADMIN | rules | server numbering |
| Open/close session | OWNER/ADMIN | rules | close audited |
| Create trip in session | OWNER/ADMIN | rules/CF for number | tripNumber CF-authoritative |
| Assign driver/labour to trip | OWNER/ADMIN | rules | may notify driver |
| Update trip status | OWNER/ADMIN; driver via CF | CF validates state | notify |
| Record attendance | OWNER/ADMIN (offline queue) | rules/CF | write + audit |
| Confirm own attendance (driver/labourer) | self | CF | if D-1 |
| Read own attendance history | self | rules | |
| View reports | OWNER/ADMIN | rules | driver limited own |
| Backup export / restore | OWNER | CF + Storage | encrypted |
| Send admin notification | OWNER | CF | |
| Soft-delete record | OWNER | CF | audited |
| Account deletion | self/OWNER | CF | audited; guards on owner |

## 3. Cross-cutting behaviours
- Every privileged/CF op writes an `auditLogs` doc (actor, action, target, prev/next, reason).
- Every business op defines whether it emits an FCM notification.
- Retry: idempotency keys on mutating ops; outbox replayed idempotently.
- Errors: return a typed result to ViewModel → UiState (never crash, never raw Firebase to UI).

## 4. Open decisions feeding contracts
D-1 (driver/labourer ops), D-2 (ADMIN delegation), D-6 (driver status workflow), D-8 (retention/audit depth). Until resolved, use most-restrictive.

## 5. Verification
PROPOSED. Mirrors the audited Flutter behavioural "workflow" but expressed as server-enforced domain ops.
