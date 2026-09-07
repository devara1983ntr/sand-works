# Threat Model

Status: current + proposed. Commit `2dd2fe4`. STRIDE-based.

## 1. Current product (offline single-user)
Assets: work/trip/labour records (incl. names & optional phones) on device; backup files; app availability; device identity.

### Threats
| Threat | STRIDE | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| Device loss/theft exposes plaintext PII (Hive) | Information disclosure | MED | HIGH | OS sandbox; native: encrypt at rest |
| Backup file stolen/plaintext leaks names/phones | Information disclosure | MED | MED | Secure backup location; native: encrypted/signed |
| Malicious crafted `.labourbackup` injects arbitrary records on restore | Tampering | LOW | MED | Structural validation + count check (exists); native: integrity/authenticity |
| Repo keystore exposure (SEC-1) → signing-key theft → malicious signed builds | Spoofing/Repudiation | MED (already occurred) | HIGH | Rotate key; purge history; Play App Signing |
| Reverse-engineering of local DB | Information disclosure | MED | LOW-MED | Encryption at rest (native) |
| App process crash mid-restore corrupts DB | DoS/data-loss | LOW | MED | Snapshot+rollback (exists) |
| No updates if no distribution channel | Availability | LOW | LOW | Not in current threat control |

Attacker profile: device thief, casual reverse-engineer, person with a crafted backup. No network attacker (no network).

## 2. Native product (multi-user + backend) — PROPOSED
New assets: user accounts/roles, cloud Firestore/Storage data, notifications, audit logs.
### New threats & required controls
| Threat | Control |
|---|---|
| Unauthorized user reads others' data (IDOR) | Firestore rules scoping reads by role/ownerId; never trust client ids blindly |
| Client claims `isAdmin=true` | Backend-authoritative role (custom claims/role doc); rules check auth.uid role |
| Privilege escalation (labourer→admin) | Server-side role assignment only via Cloud Functions/admin |
| Stolen/leaked FCM token or session | Token lifecycle mgmt; App Check; secure token storage (Keystore) |
| Firestore/Storage abuse (quota, over-read) | Rules + rate limits + Cloud Functions caps |
| Replay / race on trip/status updates | Transactions + idempotency keys server-side |
| Admin actions without trace | Audit log collection written by backend only |
| Notifications abuse | Cloud Functions to send; role-scoped topics; auth checks |
| Malicious client writes invalid domain data | Cloud Functions/security rules validate domain invariants |
| Backup/DR compromise | Authenticated, rule-protected Cloud Storage; retention; restore verification |

## 3. Data-flow trust boundaries (native, PROPOSED)
Client → (App Check) → Firebase Auth → Firestore/Storage (rules) / Cloud Functions (authz+validation) → backend systems. Client never bypasses rules.

## 4. Open items
- OQ-7 Whether driver/labourer roles get their own login (scope/consent). UNVERIFIED
- OQ-8 Required granularity of audit logging & retention. UNVERIFIED
