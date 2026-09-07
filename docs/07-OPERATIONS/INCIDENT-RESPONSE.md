# Incident Response

Status: current + proposed. Commit `2dd2fe4`.

## Current (VERIFIED)
No formal incident-response program (offline app, no backend, no monitoring). User-level incidents today:
- Corrupt local data → recovery path is restore from `.labourbackup` or clear data (data loss).
- Restore failure → app already rolls back to snapshot (good).
- App uninstall/mismatched signature → data loss unless `.labourbackup` was exported.

## Native incident-response plan (PROPOSED)
1. **Detection:** Crashlytics alerts, backend error rate, monitoring dashboards, admin reports.
2. **Severity/roles:** define on-call owner/admin; triage matrix (data integrity, security, availability).
3. **Response:**
   - Security (e.g., leaked key, auth compromise): rotate credentials; revoke sessions; purge; notify.
   - Data integrity: rely on transactional writes; restore from authenticated cloud backup; reconcile offline queue.
   - Availability: staged rollback to prior release artifact.
4. **Communication:** internal runbook + user-facing notes.
5. **Post-incident:** root-cause record, changelog, tests for regression, update rules/runbooks.

## Backup/recovery reference
See `BACKUP-RECOVERY.md`.

## Verification status
Current program VERIFIED absent. Plan PROPOSED.
