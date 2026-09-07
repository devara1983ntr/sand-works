# CI/CD

Status: current (VERIFIED: none in repo) + native pipeline (PROPOSED). Commit `2dd2fe4`.

## Current
- **No CI/CD configuration** in the repository (no `.github/workflows`, no `.gitlab-ci.yml`, etc.).
- Development commands are documented (`flutter pub get`, `analyze`, `test`, `build apk --release`).
- Prior RC builds appear to have been performed manually/agent-side; release artifacts and signing rely on local `key.properties`.

## Current gaps
- No automated quality gate, no secret scan, no artifact verification pipeline.
- No build matrix for signing/versioning.

## Native CI/CD (PROPOSED)
Pipeline (with production secrets from secure CI secret storage — never commit credentials):
```
checkout
→ dependency validation
→ static analysis (Detekt/lint)
→ format check (ktlint)
→ unit tests
→ integration tests (incl. Firebase emulator)
→ Compose UI tests
→ security checks (gitleaks/SAST; dependency audit)
→ release build (debug + release)
→ artifact verification
→ optional Firebase/Play deployment (staged)
→ release approval
```
- Secrets in CI secret store: upload keystore, keystore passwords, Google Play service account, Firebase config.
- App Check, Crashlytics enabled for release.

## Verification status
VERIFIED absence of current CI. Pipeline is PROPOSED.
