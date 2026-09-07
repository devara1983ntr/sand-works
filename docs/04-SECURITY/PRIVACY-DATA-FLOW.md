# Privacy & Data Flow

Status: current (VERIFIED) + native (PROPOSED). Commit `2dd2fe4`.

## 1. Personal data inventory (current, VERIFIED)
| Data | Type | Where | Sensitivity |
|---|---|---|---|
| Labourer name | String | `labour_box` + backups | Personal |
| Labourer phone (optional) | String? | model exists; **no UI captures it** | Personal (contact) |
| Driver name | String (on Trip) | `trip_box` + backups | Personal |
| Work/trip metadata (date, place, notes, times) | String/DateTime | boxes + backups | Business/operational |
| No geolocation, no DOB, no government IDs, no images of people, no payment data | — | — | N/A |

## 2. Data flows (current)
```
UI ──> Bloc ──> Usecase ──> Repository ──> Hive (device sandbox)
Settings ─> export .labourbackup (plaintext JSON) ─> user-chosen storage (SAF)
Settings <─ pick .labourbackup <─ restore → Hive (snapshot+rollback)
```
No data leaves the device via network. The only external flow is the user-exported backup file and the app-support directory contents if the device is backed up/migrated by OS tooling.

## 3. Privacy risks (current)
- PR-1 (MED): Plaintext PII in Hive and backups; unencrypted.
- PR-2 (LOW): Restore file could contain arbitrary records (data authenticity).
- PR-3 (INFO): No privacy policy/consent mechanism or data-deletion UX (single offline device; deletion = uninstall/clear data or full restore).
- PR-4 (INFO): Error messages embed raw exception text (minor data leakage in logs).

## 4. Proposed native data flow (PROPOSED)
```
Owner/Driver/Labourer (App) --> App Check --> Firebase Auth (UID)
   --> Firestore/Storage (rules by role+owner) --> Cloud Functions (authz/validation)
Audit log (backend-only writes); FCM (role-scoped) via Cloud Functions.
Offline queue on device --> reconcile on reconnect (server-validated).
```
- Data minimization: collect only required fields; do not add biometrics/excess PII.
- Retention & deletion: define per-data-class retention; user/account deletion path that cascades via Cloud Functions respecting business/regulatory holds.
- Consent/privacy notice before enabling optional analytics.
- Encryption at rest for local PII; authenticated cloud backup.

## 5. Required privacy artifacts (native)
Privacy policy, data-processing notes for operators, consent for notifications/analytics, deletion & export (account data portability) flows. Marked PROPOSED/not-yet-produced.

## 6. Verification status
VERIFIED current inventory & flows (no network). Native privacy program is PROPOSED and absent today.
