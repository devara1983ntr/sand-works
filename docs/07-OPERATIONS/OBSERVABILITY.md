# Observability

Status: current (VERIFIED: minimal) + native (PROPOSED). Commit `2dd2fe4`.

## Current state (VERIFIED)
- No production logging, no analytics, no crash reporting, no performance monitoring, no audit log.
- Offline app; no backend to monitor.
- No structured logging framework; any debug output is absent (`grep print` in `lib/` → none). Errors surface as UI text/snackbars only.
- No secrets logged (nothing logs).

## Native observability (PROPOSED)
- Crashlytics for crash monitoring (non-PII).
- Structured logging (e.g., timber) with severity; **never log passwords, tokens, auth headers, PII, private docs, security secrets.**
- Optional, privacy-compliant, opt-in Analytics only where it provides real value.
- Backend: Cloud Functions/Firestore metrics; monitor notification delivery (FCM); audit logs for admin actions.
- Performance: macrobenchmark results in CI; track crash-free sessions, cold start, jank.

## Logging rules
Prohibit logging of: passwords, tokens, authentication headers, sensitive personal data, private documents, security secrets.

## Verification status
VERIFIED absence today. Native program PROPOSED.
