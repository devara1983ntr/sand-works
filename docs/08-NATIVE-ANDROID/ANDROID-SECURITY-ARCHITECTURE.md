# Android Security Architecture

Status: PROPOSED (Phase 0). Covers auth, authorization, RBAC, rules, App Check, secure storage, Keystore, network, logging, secrets, components, deep links, file access, permissions, screenshots, backups, deletion, privacy. Derived from the audit's security findings (SEC-1.., SA-1, ST-*).

## 1. Foundational requirement (from audit)
Before any native release, remediate the **CRITICAL audit finding**: the committed `android/app/keystore.jks.bak` must be removed from history (branches + tags) and the signing key rotated. Do not carry this defect forward.

## 2. Layers
| Layer | Mechanism |
|---|---|
| Authentication | Firebase Auth (email/password v1; phone if D-1) |
| Authorization | Firestore/Storage rules + CF; role server-authoritative (custom claim + `users.role`) |
| App authenticity | Firebase App Check (Play Integrity) on Firestore/Storage/Functions |
| Secure local storage | Android Keystore for app-managed secrets/tokens; encrypted storage for sensitive local data; plain DataStore only for non-sensitive prefs |
| Network | TLS 1.2+ via Firebase/default; network security config; no cleartext |
| Logging | structured, redact secrets/PII |
| App surface | minimal exported components; deep-link scheme allowlisted; safe intent handling |

## 3. Secure storage / local data policy
- What may be in **DataStore**: non-sensitive preferences, last-known UI prefs, feature flags (from Remote Config).
- What needs **encrypted storage**: cached auth-adjacent tokens if persisted beyond SDK, encrypted offline backup key material, any sensitive cached records — evaluate EncryptedSharedPreferences/SQLCipher for cached PII (labourer names/phones) at rest.
- What must **never** be stored locally: Firebase service-account/admin credentials, other users' role-escalation data, raw passwords, bulk PII not required for function.
- Room stores the offline outbox/cache; encrypt sensitive columns/tables or the DB where justified (D-8 privacy).

## 4. Logout & account-switch cleanup
Sign-out clears: session token, outbox **with user consent** (or preserves per-account queues), cached other-user data, notification cache. Account switch isolates per-account data; never leak prior user's cache to next user. Cache invalidation on role/org change.

## 5. Keystore & crypto
- Android Keystore (non-exportable) for any signing/encryption keys; no keys in source or DataStore.
- No hardcoded credentials/owner account (Ramesh Sahu is a business identity, provisioned server-side).

## 6. Deep links / intents
- Deep links handled via Navigation; each deep link target re-validates the resource is visible to the current user (rules) before showing; never reveal data via link payload.
- No exported components beyond the launcher/main activity + deep-link handlers with allowlisted hosts.

## 7. Screenshots & privacy
- FLAG_SECURE only where genuinely sensitive screens require it (e.g., if showing other users' contact lists); not blanket (hurts UX). Decision D-8.

## 8. Backups
- Cloud (auto) backup: exclude sensitive local caches by default; evaluate per D-8. Offline `.labourbackup`-style export should be encrypted + authenticated.

## 9. Deletion & data privacy
- Account deletion via CF with audit + dependent-record handling; user data export request path; org data deletion policy (D-8). No silent purge.

## 10. Security review checklist (Phase-0 §46 — attack scenarios)
Must be tested in emulator/dev (never production): privilege escalation, IDOR (direct doc access by another uid), client-controlled role escalation, unauthorized reads/writes, cross-user data access, insecure Storage access, token misuse, account-takeover paths (password reset, replay), notification abuse, excessive permissions, sensitive local storage, debug leakage. Documented as test cases in `ANDROID-TESTING-ARCHITECTURE.md`.

## 11. Verification
Conceptual; actual config at implementation. Tie to ADR-007..010, `FIREBASE-SECURITY-RULES-DESIGN.md`.
