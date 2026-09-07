# DIRECTIVE REGISTER — Decision Resolution (SAND WORKS)

Resolves the previously open decisions against the locked directive, plus the new decisions this directive introduces. Classifications: RESOLVED (locked by directive) · RECOMMENDED-DEFAULT (technical basis, awaiting owner sign-off) · BLOCKED (needs environment/owner input that is genuinely absent; never guessed).

## Resolution of prior D-1..D-8
| ID | Question | Resolution | Source |
|---|---|---|---|
| D-1 | Driver/labourer self-service? | **RESOLVED — IN V1.** Driver & Labourer are authenticated app users with role-scoped capabilities. | Directive §3–5 |
| D-2 | Admin delegation? | **RESOLVED — NO admin.** ONE owner only. No ADMIN role. | §3, §47 |
| D-3 | Legacy Flutter data migration? | **RESOLVED — NO. START FRESH.** Do not migrate Hive; clean native data model. | §43 |
| D-4 | Package/brand/application identity? | **RESOLVED** — package `com.roshan.sandworks`; brand SAND WORKS; display name "SAND WORKS"; never `com.roshan.labourparty`. | §42 |
| D-5 | Session boundary (morning/evening)? | **SUPERSEDED by this scope** — not a hard requirement here; trips are date+time based (§9). Retain date/time trip recording; no fixed AM/PM session gate. | §9 |
| D-6 | Driver work-status workflow? | **RESOLVED** — drivers create/edit permitted trips directly; trip has status field; labour assignment selectable. No separate admin "driver workflow" gate. | §4, §9 |
| D-7 | Reports scope? | **RESOLVED** — PDF (primary human-readable) + CSV (raw), owner-only; multi-tractor/driver/labour/date breakdown. | §22, §36 |
| D-8 | Retention/privacy? | **RESOLVED (defaults)** — private/family use; no public release; PII is family/operator data; no analytics PII; profile photos in Storage (Blaze) with size/MIME/dimension rules. Confirm exact retention window with owner (non-blocking). | §3, §20 |

## New decisions this directive locks
| SW-ID | Decision | Value |
|---|---|---|
| SW-1 | Owner identity | Ramesh Sahu (single owner) |
| SW-2 | Default trip rate | ₹200 per trip; owner-configurable; per-trip snapshot immutable after record |
| SW-3 | Money distribution default | Equal split among driver + eligible(present) labourers on the trip; configurable rule (equal / driver+labour share / custom % / fixed allocation); integer currency; no float |
| SW-4 | Daily summary time | Default 19:30 IST, configurable within 7–8 PM window |
| SW-5 | Daily closure | Server-authoritative; idempotency boundary = (date + organization); exactly once; audit; never labels "Payment completed" (accrued total only) |
| SW-6 | Payment semantics | App tracks ACCRUED totals only; physical payment out of scope; wording "Today's earnings added"/"summary" |
| SW-7 | Temporary labour assignment | Owner/authorised driver may assign a labourer to an operational role for a limited period (start/end, reason, scope, status); expiry is enforced by BACKEND authz; no permanent escalation |
| SW-8 | Initial tractors | Sonalika + John Deere as initial OWNER-registered records; NOT hardcoded logic — OWNER-managed tractor registry |
| SW-9 | Trip number | Backend-authoritative, collision-safe; never client-only |
| SW-10 | Leaderboard | Weekly (reset each week) + monthly (reset each month), top 3 only, deterministic tie-break, based on real persisted trips; if fewer eligible → show only real ranks, never fabricate 2nd/3rd |
| SW-11 | WhatsApp share (driver) | Share today's trip count via standard share intent; WhatsApp-optional (share-sheet fallback); real values only |
| SW-12 | Labourer operational model | READ-ONLY operationally (profile/stats/leaderboard/notifications) |
| SW-13 | User approval | New driver/labourer registration requires OWNER approval; no privileged access until approved; backend-enforced |
| SW-14 | Alerts | Owner-only "operational warning"; recipients drivers/labourers/approved users; high-priority, vibration, custom alert sound, heads-up where permitted, prominent UI + ack; **never claim full-volume override of silent/DND; no unsafe volume tricks; restrict full-screen intents** |
| SW-15 | Colour/type/design | Locked design language + palette + Roboto (see UX-DESIGN-SYSTEM.md) |
| SW-16 | Signing | New dedicated native release keystore (never legacy Flutter keystore); env/ignored-local credentials; same key for all future private APK updates; never commit jks/keystore/key.properties |
| SW-17 | APK size | Target ~15–25 MB release, acceptable ≤50 MB; never sacrifice security/reliability/a11y/data-integrity for size |

## Remaining BLOCKED (genuine; not disguised as tasks)
| BLK | Class | What is missing | Smallest input needed |
|---|---|---|---|
| SW-BLK-1 | CREDENTIAL/ENVIRONMENT | Real Firebase project + google-services + env | Provide Firebase project; never commit keys |
| SW-BLK-2 | ENVIRONMENT (plan) | **Firebase Blaze plan** required for Cloud Storage AND Cloud Functions (server-authoritative scheduling/closures) | Owner decides Blaze vs Spark; if Spark, we do NOT fake Storage/scheduling — documented fallbacks per §20/§12 |
| SW-BLK-3 | SECURITY/ENV | New dedicated release signing identity | Generate keystore; store in env/ignored file; offline backup |
| SW-BLK-4 | ASSET (only if needed) | Masters are present at repo root; if Android launcher density copies are desired they must be generated FROM masters (allowed transformation), not re-authored | (none blocking — launcher generation is mechanical from masters) |

## Non-blocking confirmations (do not gate build, but nice to confirm)
- Exact data retention window (D-8) for audit/history.
- Whether owner wants cloud backup (Blaze) vs local-only backup in V1.
- Notification channels content/copy wording (approved copy only).
- Labourer self-registration flow vs owner-created accounts (directive says "registration requires approval", implying self-registration exists → confirm self-signup enabled).
