# Backup & Recovery

Status: current (VERIFIED) + native (PROPOSED). Commit `2dd2fe4`.

## Current product backup (VERIFIED)
- **Manual** export: Settings → Backup Database → SAF save of `.labourbackup` (JSON; `version:1`, app `"Labour Party"`, all 4 collections; ≤25 MB).
- **Manual** restore: pick file → validate (extension, size, structure, bounds) → overwrite-confirm dialog → snapshot→clear→apply→verify counts→rollback on error.
- Recommended cadence (from SOP): before high-risk changes & weekly; export before device migration.

## Current recovery realities (VERIFIED)
- No automated/scheduled backup.
- No cloud backup; single device is the store of truth.
- No verification that a backup restores to a *new* device beyond manual flow (restore logic tested indirectly).
- Backup is plaintext & unsigned (BUG-13/15).
- No retention policy; backups & Hive data grow unbounded.

## Native backup & recovery (PROPOSED)
- **Firestore:** enables server-side data as an online copy with offline-first local persistence (retention/cleanup policy defined).
- **Cloud Storage backup:** periodic, authenticated export with App Check & rules; retention & lifecycle management.
- **Recovery:** 
  - Accidental deletion: audit log + recycle/soft-delete where appropriate; restore from backup.
  - Administrative recovery: Cloud Functions/admin tools to restore a user/dataset.
  - Incident recovery: see INCIDENT-RESPONSE.
- **Local `.labourbackup`:** retain as an offline/portable export but make it encrypted/signed (native).
- **Retention:** define data-class retention (records vs audit vs PII) and deletion requests.

## Do-not-claim rule
Do not claim backups exist beyond the manual `.labourbackup` flow unless automated/verified. Native cloud backup is PROPOSED, not present.

## Verification status
VERIFIED: manual backup/restore only. Automated/cloud backup PROPOSED & absent.
