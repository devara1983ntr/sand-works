# Storage Analysis (current)

Status: VERIFIED. Commit `2dd2fe4`.

## Storage tiers
| Tier | Technology | Content | Lifetime |
|---|---|---|---|
| Primary data | Hive boxes (`work/trip/labour/trip_labour`) | all domain records | persists across restarts; cleared only by uninstall/restore |
| Draft | Hive `draft_box` (`current_draft`) | in-progress new trip | overwritten on save; cleared on success |
| Preferences | (none custom) | — | not used (no theme prefs, etc.) |
| Export | `.labourbackup` file (SAF) | JSON snapshot of 4 boxes | user-managed |
| Assets | `assets/branding/app_icon_192.png` | brand icon | bundled |

## Data volume & performance design
- Single-user daily-use scale: small; but long-term history can grow. Design uses composite keys & prefix scans to avoid full-box scans for trip attendance, and `putAll` bulk writes for trips with many labours.
- Full scans still used for `getWorks`, `getAllTrips`, `getLabours`, `getLaboursForTrips` (filter over values). Acceptable at current scale; watch for growth (see `05-PERFORMANCE/`).

## Encryption at rest (VERIFIED — none)
- Hive boxes are **not encrypted**; no keychain/keystore use. PII (names, optional phones) stored plaintext inside the Android app sandbox.
- Backup `.labourbackup` is plaintext JSON (no encryption/signature). Restore trusts file content after structural checks; a maliciously crafted file could inject arbitrary records (client-only risk).

## Backup format (VERIFIED)
`{ "version":1, "createdAt":ISO, "app":"Labour Party", "data":{ works:[], trips:[], labours:[], tripLabours:[] } }` — each record as plain maps with ISO datetimes. No integrity hash; version field is informational (only null-checked).

## Backup/restore constraints
- Size ≤ 25 MB; row bounds works≤10000, trips≤100000, labours≤5000; count verification after restore; snapshot rollback on failure.
- Storage Access Framework used → no broad storage permission.

## Findings
| ID | Severity | Finding |
|---|---|---|
| ST-1 | MEDIUM | No encryption at rest for PII |
| ST-2 | MEDIUM | Backup is unencrypted/unsigned plaintext PII |
| ST-3 | LOW | Draft `encodedLabours` JSON-in-string |
| ST-4 | LOW | No retention/purge policy; backups & local data grow unbounded |
| ST-5 | INFO | Restore trusts content post-structural-validation (no authenticity check) |

## Proposed native
Evaluate Room/Firestore with encryption (SQLCipher if local), authenticated + encrypted backup (Cloud Storage with rules/App Check), retention policies, audit trail. See `08-NATIVE-ANDROID/`.
