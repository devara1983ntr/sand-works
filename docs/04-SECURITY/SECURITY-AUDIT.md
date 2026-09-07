# Security Audit (current)

Status: VERIFIED (static). Severity: CRITICAL/HIGH/MEDIUM/LOW/INFO. Commit `2dd2fe4`.

## Findings
| ID | Sev | Finding | Evidence | Remediation |
|---|---|---|---|---|
| SEC-1 | CRITICAL | Release-keystore-like backup (`keystore.jks.bak`, DER/PKCS#8 v0 structure) committed to repo | tracked file; added commit `55b2144`; `.gitignore` misses `*.jks.bak` | Rotate/regenerate signing key; remove file from history; expand .gitignore; if it is a live key, treat as compromised & revoke Play key via Play App Signing reset |
| SEC-2 | MEDIUM | Local data & backup unencrypted PII (labour names, optional phone) | Hive plaintext; plaintext `.labourbackup` | Encrypt local PII at rest (native); signed/encrypted backup |
| SEC-3 | MEDIUM | Restore trusts file contents after structural checks (no authenticity/owner integrity); could inject arbitrary records | `_parseAndValidateBackup` | Add integrity signature / authenticator of backups |
| SEC-4 | LOW | No output sanitization needed (no HTML/webview), but error messages concatenate raw exception text shown to user | repo impls `...$e` | Provide clean, non-internal error text |
| SEC-5 | LOW | Duplicate `@override`/duplicate `switch` cases indicate risky manual merges | data source, repo impl, trip_details | Clean; add lint/test gate |
| SEC-6 | INFO | No authentication by design; not a vuln today but becomes one if app is opened up to multi-user without adding authz | whole app | Native must add auth + server authz |
| SEC-7 | INFO | No crash reporting/analytics (offline) — no telemetry risk, but also no visibility | none | Native adds Crashlytics w/ non-PII config |

## In scope checks with no issue found
- Injection: no SQL (Hive only); no code-eval paths; form strings used as data.
- WebViews: none present.
- Exported components: only launcher MainActivity.
- Deep links: none.
- Network/TLS: not applicable (no network).
- Token/session storage: not applicable (no auth).

## Positive
- No runtime API keys/secret strings in `lib/`; `key.properties` correctly git-ignored; release task fails safely if key.properties missing.

## Verification status
Static VERIFIED. Runtime/pen-testing not possible in this environment (`UNVERIFIED` dynamic).
