# CI/CD Architecture

Status: PROPOSED (Phase 0). No CI exists in the repo (VERIFIED). Design with production secrets in CI secret storage — never commit credentials.

## 1. Environment strategy
| Env | Firebase project | Usage | Rules |
|---|---|---|---|
| development | separate dev Firebase project | dev build, emulator, local CI | debug never points to prod |
| staging | separate staging project | release-candidate verification, emulator-acceptance | test data only |
| production | production project | end-user | App Check; no destructive tests |

- Bind each build type to the correct Firebase project via `google-services.json` build-type/firebase config. **Debug builds must never connect to production services.**
- Do not invent actual project IDs (D-4); use placeholders in config, verify at provisioning.

## 2. Pipeline
```
Pull Request
  → Formatting (ktlint)
  → Static Analysis (detekt/lint/Android Lint)
  → Unit Tests
  → Firebase Emulator tests (Security Rules + CF)
  → Compose UI tests
  → Security checks (secret scan gitleaks; dependency audit; SAST)
  → Debug build
  → Release build (unsigned/R8)
  → Artifact verification (install+smoke on clean emulator)
  → Staging
  → Acceptance (staging project; emulator; manual+automated)
  → Production (requires explicit approval/owner sign-off)
```
Production deployment gated on explicit approval (OWNER).

## 3. Secrets & signing
- Keystore + keystore passwords + Play service account in CI secret store only.
- Play App Signing; upload key not in repo (remediation of audit SEC-1).
- google-services.json for prod not committed in a way that leaks to debug builds.

## 4. Artifacts & release
- versionCode/versionName automated; release notes; staged rollout; rollback path (prior artifact); post-release crash monitoring.

## 5. Quality gates
Enforce gate from `06-QUALITY/PRE-RELEASE.md` + emulator rules tests + no-CRITICAL/HIGH open.

## 6. Verification
PROPOSED. No CI implemented.
