# Firestore & Storage Security Rules — Design

Status: PROPOSED conceptual rules (Phase 0). No rules are deployed. These define WHO can do WHAT on WHICH doc under WHAT conditions. Production rules must be written and tested against the **Firestore Emulator** before deployment (Firebase recommendation).

## 1. Principles
- Default deny.
- Every sensitive op: (actor, verb, resource, condition).
- Authorization checks `request.auth.uid`, role (custom claim or `users` doc), and ownership (`orgId`, creator, assignee, labourerId). Role resolved server-side; never trust client fields.
- Never: `allow read, write: if request.auth != null;` as strategy.
- Cloud Functions for ops rules can't express (role change, audit write, destructive delete, notifications, authoritative numbering).

## 2. Helpers (conceptual)
```
function signedIn(){return request.auth!=null;}
function isAdmin(orgId){
  return signedIn() && (
    request.auth.token.role in ['owner','admin'] &&
    request.auth.token.org == orgId);
}
function isMember(orgId){ return signedIn() && request.auth.token.org == orgId; }
function self(){ return request.auth.uid; }
```

## 3. Firestore rules (sketch — definitive version lives with implementation + emulator tests)
| Resource | read | write | Condition notes |
|---|---|---|---|
| users/{uid} | self, or OWNER/ADMIN of org | create self; update self limited profile; role/status via CF | no client role change |
| drivers/labourers | OWNER/ADMIN (org) | OWNER/ADMIN | soft-delete only |
| workSessions | OWNER/ADMIN; drivers see own assigned | create OWNER/ADMIN; close OWNER/ADMIN(admin delegated) | |
| trips | OWNER/ADMIN; assigned driver (self-service) | create/update OWNER/ADMIN; driver status via CF | |
| attendance | OWNER/ADMIN; worker self | record OWNER/ADMIN; worker read-only | worker cannot modify others |
| notifications/{userId} | self only | self toggles read; server creates | |
| auditLogs | OWNER/ADMIN | CF/admin only (deny client) | |
| settings | members scoped | OWNER | |

Example gating an owner admin user-management is done in a CF (not direct Firestore), because role/status changes + audit must be atomic and privileged.

## 4. Storage rules (sketch)
- Profile photos: `orgs/{orgId}/users/{uid}/...` — read members (scoped), write self (owner of that uid path) or OWNER/ADMIN. Allowlist content types/size.
- Encrypted backups/exports: `orgs/{orgId}/backups/...` — read/write OWNER/ADMIN only; App Check.
- Never a public bucket.

## 5. Emulator test matrix (must cover)
Owner CRUD; admin delegation; driver read-own-only; labourer read-own-only; labourer attempting to modify another's attendance = DENY; user attempting role escalation = DENY; cross-org access = DENY; unauthenticated = DENY; Storage unauthenticated/abuse = DENY.

## 6. App Check
Enforce App Check on Firestore/Storage/Functions. Handle attestation failures gracefully (deny, surface app-update message) without DoS of legitimate users.

## 7. Replay / abuse / rate limiting
Functions enforce quotas & idempotency; alerting on anomalies; no silent overwrite of user data.

## 8. Anti-patterns (explicit)
Never trust `isAdmin`, `role`, `permissions`, `owner` from client. No broad allow. No unbounded nested reads via rules without size guards.

## Verification
Conceptual only; actual rules authored & tested in emulator during implementation. Security review (Phase-0 §46) enumerated in `ANDROID-SECURITY-ARCHITECTURE.md`.
