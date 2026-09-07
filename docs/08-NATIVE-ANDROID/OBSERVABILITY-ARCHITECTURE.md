# Observability Architecture

Status: PROPOSED (Phase 0). Current Flutter app has no telemetry (VERIFIED). Native adds privacy-conscious observability. Do not collect unnecessary personal data.

## 1. Layers
| Layer | Mechanism | Notes |
|---|---|---|
| Crash | Crashlytics | fatal/non-fatal, non-PII breadcrumbs |
| Structured app logs | Timber-like wrapper with levels | no secrets/PII; debug-only verbose |
| Auth monitoring | Firebase Auth events (sign-in success/failure, reset) | aggregate; watch for brute force |
| Backend logs | Cloud Functions logging | redact; structured |
| Audit logs | `auditLogs` Firestore (see FIREBASE-DATABASE-DESIGN) | privileged actions only |
| Notification delivery | FCM send result + CF metrics | token errors, failures |
| Performance | (optional) Crashlytics custom traces / AndroidX metrics for startup & key flows | privacy-reviewed; do not over-collect |

## 2. Logging rules (mandatory)
Never log: passwords, tokens, auth headers, secrets, full PII (names/phones), private docs, notification content. Use correlation IDs not PII where possible.

## 3. Analytics
Only if it provides genuine product value AND is opt-in/privacy-compliant (default off). Not added for show.

## 4. Alerting thresholds
Define for error rate, crash-free sessions, sign-in failures, CF error rate, FCM failure rate. (Values set at implementation/ops with product.)

## 5. Backend service health
Firestore quota/usage, CF invocation errors, scheduled-job health, FCM. Surfaces to operator/admin reporting (owner).

## 6. Verification
PROPOSED. Current telemetry absent (VERIFIED).
