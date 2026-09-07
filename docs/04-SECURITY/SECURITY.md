# Security Overview (current + target)

Status: current VERIFIED; native controls PROPOSED. Commit `2dd2fe4`.

## Executive position
The **current** product has a very small attack surface because it is **offline, single-user, local-only, unauthenticated**, with no network, no backend, and no secrets required at runtime. Its main risks are local/data-level and configuration-level (a committed keystore-like file). It is **not** a multi-user/RBAC product today.

The **native** product introduces accounts, roles, a backend, notifications, and cloud data — a fundamentally larger trust boundary that requires server-authoritative security. This must be designed in from the start, not bolted on.

## Guiding principle (applies to native)
> **«Client-side role checks are NOT sufficient authorization.» The backend must enforce authorization.**

## Current security posture (VERIFIED)
| Area | Status | Notes |
|---|---|---|
| Authentication | NONE (by design) | single local user |
| Authorization | NONE | all features available to the app user |
| RBAC | NONE | no roles exist |
| Network exposure | NONE | no INTERNET permission; no sockets/http |
| Secrets in runtime code | NONE | client holds no keys |
| Secrets in repo | **YES (CRITICAL)** | `android/app/keystore.jks.bak` committed (see SECRETS-AUDIT) |
| Encryption at rest | NONE | Hive plaintext |
| Backup security | Weak | plaintext JSON `.labourbackup`; structural validation only |
| Input validation | Present (form validators, backup parse) | client-side |
| Deep links/exported components | Minimal | launcher only; no exported receivers |
| Logging | No production logging/analytics | offline |

## Target security architecture (PROPOSED)
- Firebase Authentication (owner/admin + optional driver/labourer).
- Backend-authoritative roles (custom claims or role documents/Cloud Functions) — never client `isAdmin=true`.
- Firestore + Storage security rules; App Check; Cloud Functions for privileged/transactional ops.
- Audit logging for admin/privileged actions.
- Encryption at rest for local PII where applicable; authenticated & rule-protected cloud backup.
- Enforce: IDOR protection, privilege escalation prevention, rate limiting, replay controls.

See `04-SECURITY/THREAT-MODEL.md`, `SECURITY-AUDIT.md`, `AUTHENTICATION-AUTHORIZATION.md`, `RBAC.md`, `SECRETS-AUDIT.md`, `PRIVACY-DATA-FLOW.md`.
