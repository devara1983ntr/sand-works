# Monitoring

Status: current (VERIFIED: none) + native (PROPOSED). Commit `2dd2fe4`.

## Current
No monitoring exists: no crash dashboards, no analytics console, no alerting, no uptime (no service). The app cannot surface silent failures to a remote operator (by design, offline). Issue visibility depends on the user reporting.

## Native (PROPOSED)
- Crashlytics (fatal/non-fatal) with issue alerting.
- Firebase Performance / custom trace for startup & key flows (privacy-reviewed).
- Backend monitoring: Firestore usage/quota, Cloud Functions errors, FCM delivery metrics, scheduled-job health.
- Alerting thresholds for error rate, crash-free sessions, sign-in failures.
- Admin/audit dashboards for privileged actions.

## Verification status
VERIFIED absence. Native plan PROPOSED.
