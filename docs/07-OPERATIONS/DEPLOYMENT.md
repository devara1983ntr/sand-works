# Deployment

Status: current (VERIFIED process from repo/docs) + native (PROPOSED). Commit `2dd2fe4`.

## Current release engineering (VERIFIED)
- Build: `flutter build apk --release` requires `android/key.properties` (release signing). Release build type: `minifyEnabled=true`, `shrinkResources=true`, proguard file present (`android/app/proguard-rules.pro`).
- Application id: `com.roshan.labourparty`; version `1.0.0+1` (from pubspec).
- Signing: keystore config via `key.properties` (not committed); only `key.properties.example` committed. **Keystore-backup file is wrongly committed (BUG-01).**
- Distribution: prior release candidates tagged (`v1.0.0`, `v1.0.0-rc1`, `v1.0.1-hotfix-rc1`, `RC-1.3`). No in-repo evidence of a specific Play console/app-store deployment record (see CODEBASE note; `UNVERIFIED` whether app is actually published).

## Release process expectations (from docs, VERIFIED content)
See `docs/RELEASE.md`: generate `upload-keystore.jks` (RSA 2048+, long validity), keep secret, create `key.properties`, build release. Data migration between mismatched signature installs requires `.labourbackup` export/restore (README).

## Native (PROPOSED) — release engineering
- Pin versioning (versionCode/versionName) with automation.
- Use **Play App Signing**: upload key kept in CI secret store / Google Play; app signing key managed by Play.
- Build variants: debug vs release (and flavours if roles/regions differ).
- R8 resource shrinking with tested rules; proguard kept.
- Staged rollout (e.g., 10% → 100%) with monitoring; rollback via prior artifact.
- Release notes + changelog; post-release verification & crash monitoring.

## Verification status
VERIFIED build/signing config. Whether a Play-store release exists `UNVERIFIED`.
