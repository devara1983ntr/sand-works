# Secrets Audit

Purpose: locate and classify any secrets/keys in the repository **without reproducing their values**.

Status: VERIFIED. Commit `2dd2fe4`.

## Policy (repeated from brief)
Absolutely prohibited: API secrets, passwords, service-account credentials in APK, private keys in repo, production tokens in config, Firebase Admin credentials in Android, hardcoded owner credentials.

## Findings
| ID | Sev | Location | Description | Action |
|---|---|---|---|---|
| SA-1 | CRITICAL | `android/app/keystore.jks.bak` | 2,690-byte **tracked** file. Hex prefix `30 82` = ASN.1 DER SEQUENCE; contains a PKCS#8 version-0 marker (`02 01 00`). Filename implies a keystore/signing-key **backup**. `.gitignore` excludes `*.jks`/`*.keystore` but NOT `*.jks.bak`. Added in commit `55b2144` and **present in every tag** (`v1.0.0`, `v1.0.0-rc1`, `v1.0.1-hotfix-rc1`, `RC-1.3`). | Do NOT treat as benign. (1) Determine whether this is an active signing/upload key. (2) Regenerate & rotate the key. (3) Purge the file and its history across all branches/tags (e.g., `git filter-repo` + tag rewrite). (4) Harden `.gitignore`. If it is the Play upload key, use Play App Signing to reset the key. Values are NOT reproduced in this audit. |
| SA-2 | NONE | `android/key.properties` | Correctly **not committed** (absent). Only `key.properties.example` (blank template) is tracked. | None — good practice. |
| SA-3 | NONE | source code (`lib/`) | No hardcoded credentials/owner password. No owner name present. | None. |
| SA-4 | INFO | `android/app/proguard-rules.pro` | `-keep class com.roshan.labourparty.**` | Not a secret; confirm intended for R8. |

## Confirmed clean (VERIFIED)
- No Firebase/Admin JSON, no `.p12`, no `.env`, no service-account files, no API tokens found under `lib/`, `android/` (other than SA-1), `assets/`.
- No hardcoded owner credentials anywhere.
- `key.properties` git-ignored.

## Remediation checklist (PROPOSED)
- [ ] Treat SA-1 as potential private-key exposure; rotate immediately if live.
- [ ] Remove SA-1 from repo & history.
- [ ] Add `*.jks.bak`, `*.keystore.bak`, `**/*.jks`, `**/*.keystore`, `key.properties*` to ignore; add a secret-scan to CI (gitleaks/TruffleHog).
- [ ] Audit other branches/tags for the file — **DONE (VERIFIED): present in all four tags.**

## Verification status
VERIFIED static: file tracked on `main` HEAD, present in all four tags, and present in ~26 of 31 remote branch heads (grep over remote refs). Purge must therefore cover branches and tags, not just `main` history.
