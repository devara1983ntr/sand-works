# Firebase Coverage Matrix

Status: Phase 0.5 (PROPOSED). For each Firebase service: required?, why, who uses, what data, security boundary, failure mode, testing, cost/ops. Do not add a service without a requirement. Extends FIREBASE-ARCHITECTURE.md.

| Service | Required? | Why | Users | Data/use | Security boundary | Failure mode | Testing | Cost/ops |
|---|---|---|---|---|---|---|---|---|
| Authentication | YES | identity/roles | all | users, email | Auth + rules (emailVerified) | network/auth errors | emulator | minimal |
| Firestore | YES | primary store | all | work/trips/attendance/crew/notif/audit | Security Rules (server-side) | offline/quota | emulator+rules | usage-based |
| Cloud Functions | YES | BO-1..16 server ops | all | server-only writes | App Check + CF identity | timeout/retry | emulator/unit | invocations |
| Cloud Storage | YES | backup/export/photos (D-3/7) | owner | backups, exports, avatars | Storage rules (owner) | size/network | emulator | egress |
| Cloud Messaging (FCM) | YES | notify | all | device tokens/topics | server token mgmt | delivery fail | integration | volume |
| App Check | YES | prevent abuse | all | attestation | blocks non-verified | false-block | emulator | included |
| Crashlytics | YES | stability | dev | crash reports | redact PII | — | — | included |
| Analytics/Firebase | (part) | product metrics | dev | events (no PII) | — | — | — | included |
| Remote Config | OPTIONAL | feature flags | dev | flags | — | — | — | included |
| Performance Monitoring | OPTIONAL | latency | dev | traces | — | — | unit | included |
| Predictions | NO | not needed | — | — | — | — | — | not added |
| ML Kit | NO | no requirement | — | — | — | — | — | not added |
| Firestore Counters/FieldValue increment | YES | aggregates | — | counters | rules | eventual | — | — |
| Cloud Scheduler | OPTIONAL | nightly report aggregates (D-7) | — | jobs | — | missed run | — | — |

## Rules applied to service decisions
- Only services with a requirement are in the plan; NO services added speculatively.
- ML Kit/Predictions omitted (no feature requires OCR/ML).
- Remote Config optional and only if a feature-flag need emerges.
- App Check added to meet abuse-prevention; security boundary on all data services = server/rules.

## Failure-mode & cost notes
- Firestore: enforce query/index hygiene to cap cost (perf risk if reports aggregate client-side → move to CF).
- FCM: token lifecycle; clean stale topics on role change; server retry with backoff.
- Cloud Functions: cold-start/latency on owner field ops acceptable; always idempotent keys.
- Storage: avatars/backups/export size policy + lifecycle rules (D-8 retention).
- Verify: services are decisions, not deployment; D-1/D-3/D-7 gate driver/cloud-backup/report jobs.
