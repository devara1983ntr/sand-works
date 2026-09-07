# SECURITY & RBAC — SAND WORKS

Backend-authoritative. UI visibility is NOT authorization. Roles OWNER/DRIVER/LABOURER; single owner; no admin. Client must never be trusted for: role, approval, money, rate, trip number, leaderboard, authorization, daily closure, audit (SERVER-authority). App Check on Firestore/Storage/Functions. No `allow read,write: if true`; no `authenticated==sufficient`.

## RBAC matrix (summary; detail in ROLE-AND-USER-MODEL)
| Operation | OWNER | DRIVER | LABOURER |
|---|---|---|---|
| Read org-scoped business data | ✓ | own scope (assigned/created/own trips) | own attendance/earnings/history |
| Create trip | ✓ | ✓ (own) | ✗ |
| Edit own permitted trip | ✓ | ✓ (own, within window/authority) | ✗ |
| Approve users | ✓ (CF) | ✗ | ✗ |
| Rate/money rules/settings | ✓ (CF) | ✗ | ✗ |
| Corrections (attendance/money) | ✓ (CF, reason+audit) | ✗ | ✗ |
| Send alert/message | ✓ (owner only) | ✗ | ✗ |
| Export PDF/CSV | ✓ | ✗ | ✗ |
| Temp assignment (create) | ✓ | ✓ (authorised, limited) | ✗ |
| Notifications | ✓ | ✓ (own) | ✓ (own) |

## Enforcement layers (rule-of-three)
1. **UI/Compose**: render actions the role may take (routing/visibility) — NEVER authorization.
2. **Firestore/Storage Security Rules**: enforce every read/write by role + org + ownership + approval/status/expiry.
3. **Cloud Functions (CF)**: perform privileged/business ops (role/approval/status changes, rate & money config, trip numbering, daily closure, leaderboard, alert send, audit writes, export, corrections, account ops). CF validates role/org/input; never a thin unauthenticated relay.

## Scoping primitives
- `orgId` on every business record; single family org.
- `role` + `approval.status` + `users.status(active)` on the auth user's doc/claim; server-enforced.
- Ownership: `driverRef`/`driverId` scope driver trip reads/writes; `labourerId`/`uid` scope labourer reads; `assignedDriverId` for assignment.
- Approval: a newly registered driver/labourer with `approval != approved` is denied all business reads/writes until OWNER approves (backend check each op; not UI-only).

## Temporary assignment expiry (SW-7)
- Elevated operational scope (labourer performing driver role) is bounded by `assignments{endDateTime/expiresAt,status}`.
- Security Rules + CF reject elevated access when `now > expiresAt` or status no longer active. Expiry is backend-enforced — no client clock trust (use server `request.time` in rules).

## Key security requirements
- No client-writable role / approval / ownerId / orgId / status / createdAt / server timestamps / createdBy / changedBy / tripNumber / money / rate / closure / audit / leaderboard / isAdmin-like field (SERVER-AUTHORITY). Client may write only validated domain content + its own `opId`.
- Audit: server-generated (CF), append-only, owner-read, no client write/delete. Retention per D-8.
- Deep links / notifications re-validate target auth + org + ownership + existence → NotFound/Forbidden fallback.
- Disabled/revoked user: rules deny; local cache cleared on disable.
- Storage (profile photos, export files): authenticated, owner/self scoped, size + MIME + dimension validation, signed URLs for export with expiry; no public reads. **Only if Blaze** (SWF-24/18 cloud) — no fake Storage on Spark.
- Alerts: owner-only; strong-compliant; no unsafe volume manipulation; restrict full-screen intents; no spoofed sender.
- Rate limiting/abuse: App Check + quotas + CF rate limits; monitoring.
- Query/Security-Rule compatibility: every Firestore query must satisfy rules (rules are not filters). Queries must include org/role/ownership predicates and skip docs rules would deny (e.g., unauthorised/deleted). Indexes declared per query.

## Tests (emulator + security)
Per FINAL TEST / quality spec: rules tests for every role × operation × approval/status/expiry cell; escalation attempts (driver→owner, labourer write, cross-org, forge role/approval/money/audit/timestamp/trip-number/closure), deep-link authz, storage rules, App Check, disabled/expired access, idempotency/conflict, correction audit. No control may be absent or weakened (AGENT §14).

## Ownership & single-org
V1 single family org; schema keeps orgId future-safe. Cross-org access impossible via rules.
