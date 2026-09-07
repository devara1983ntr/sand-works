# Pre-Release Quality Gate

Purpose: a release must NOT be declared production-ready until the gate below passes. Status: this is the required gate (applies to native and any Flutter release). Commit `2dd2fe4`.

## Gate checklist
- [ ] Build succeeds (debug + release).
- [ ] `flutter analyze` / static analysis clean (or lint gate passes).
- [ ] Unit tests pass.
- [ ] Widget/integration tests pass.
- [ ] Lint passes.
- [ ] Security checks pass (secret scan; no secrets/keys in repo/APK).
- [ ] Critical flows pass (create trip → attendance → history/analytics → backup/restore).
- [ ] Authentication works (native) / N/A for current offline app.
- [ ] RBAC works (native) / N/A current.
- [ ] Backend/Firebase authorization works (native) / N/A current.
- [ ] Notifications work (native) / N/A current.
- [ ] Offline behaviour acceptable (current = inherently offline).
- [ ] Error states work (add retry where missing).
- [ ] Accessibility checks pass (address HIGH A-1/A-2).
- [ ] Performance acceptable (measure cold start/scroll).
- [ ] No known CRITICAL/HIGH vulnerabilities remain (resolve BUG-01 + keystore before any release).
- [ ] No production secrets exist in the APK/repository (rotate + purge keystore first).
- [ ] Release signing correct (keystore in CI secrets, not repo).
- [ ] Monitoring active (native: Crashlytics) / N/A offline current.
- [ ] Crash reporting works (native).
- [ ] Database rules reviewed (native).
- [ ] Backup/recovery strategy exists & tested (current: manual `.labourbackup` verified).
- [ ] Release artifact verified (install & smoke on clean device).

## Current-product verdict vs gate
| Criterion | Verdict |
|---|---|
| The code appears release-shaped (prior RC tags) | TRUE (structural) |
| Independent execution of the gate in this environment | **UNVERIFIED** (no Flutter SDK) |
| Secret hygiene | **FAILS** (BUG-01 keystore present) |
| Production readiness claim | Deferred pending gate execution + secret remediation |

## Native
Apply identical gate plus Firebase rules review, App Check, audit logs, and macrobenchmarks before each release.
