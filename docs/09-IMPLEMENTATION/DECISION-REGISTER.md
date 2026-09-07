# DECISION & BLOCKER REGISTER (implementation layer)

> **RE-FREEZE (SAND WORKS).** The controlling decisions are now resolved by the locked SAND WORKS directive in **`docs/10-SANDWORKS/DIRECTIVE-REGISTER.md`** (D-1 roles in scope with driver+labourer as authenticated users; D-2 no admin; D-3 no legacy migration/START FRESH; D-4 package `com.roshan.sandworks` + brand SAND WORKS; D-5 superseded date+time; D-6 driver creates trips; D-7 reports PDF+CSV owner; D-8 private retention defaults). Treat this file's legacy S-V1-only entries below as superseded where they conflict; the SW-BLK-* execution blockers govern go-live.

## Execution blockers (govern go-live; see docs/10-SANDWORKS/DIRECTIVE-REGISTER.md for detail)
| ID | Decision/blocker | Why required | Blocks | Smallest input needed |
|---|---|---|---|---|
| SW-BLK-1 | Real Firebase project + credentials + google-services + env | cannot author real auth/rules/CF/Storage | Auth/security/CF phases | provide project + runbook; never commit secrets |
| SW-BLK-2 | Firebase **Blaze vs Spark** | Storage (profile photos, exports) + Cloud Functions (scheduling/closures/backup) need Blaze | SWF-24/25/26 | owner: choose Blaze; if Spark use documented fallbacks (no fake Storage/scheduling) |
| SW-BLK-3 | Dedicated release signing identity (new keystore) | private APK distribution; SEC-1 hygiene | release | generate keystore; env/ignored creds; offline backup |
| (see also) | Docs/09 legacy per-task contracts need reconciliation to SAND WORKS scope | previous 52 tasks were single-owner S-V1 | task inventory | reconcile tasks per docs/10-SANDWORKS |



Mirror of `08-NATIVE-ANDROID/FINAL-DECISION-REGISTER.md` + runtime implementation blockers. Each entry: what's missing · why required · what it blocks · smallest decision/input needed. Blocker classifications per AGENT §14.23.

## Gate-0 blockers (BLOCKED — BUSINESS DECISION unless noted)
| ID | Decision/blocker | Why required | Blocks | Smallest input needed |
|---|---|---|---|---|
| BLK-01 | Scope **S-V1** (single OWNER) confirmation; D-1 (driver/labourer self-service), D-2 (ADMIN), D-6 (driver workflow) in-V1 vs deferred | determines roles/screens/rules/DB | entire programme | owner: "roles in V1? Y/N" (if Y, scope expands materially) |
| BLK-02 | **D-4** package/applicationId/app name/branding/signing identity | update-path continuity; SEC-1 signing | IMPL-101/804; scaffold | owner provides applicationId + signing decision |
| BLK-03 | **D-3** legacy data migration need | whether real prod data must be imported | optional importer feature; import-only if confirmed | owner: "does real data exist? import? Y/N" |
| BLK-04 | Real Firebase project + credentials + google-services + signing keys | cannot author real auth/rules/CF/Storage without project | IMPL-301..307, IMPL-804 | provide project + runbook; never commit secrets |

## Additional classified blockers (appear as they arise during execution)
| ID | Task | Class | What is missing |
|---|---|---|---|
| BLK-05 | IMPL-704 | BLOCKED — MISSING ASSET | authoritative logo/brand/icon asset absent → report, do not invent |
| BLK-06 | IMPL-605 | BLOCKED — DESIGN SPECIFICATION (if content undefined) | help/FAQ copy not approved → use truthful content or block |
| BLK-07 | DEP re-verify | BLOCKED — ENVIRONMENT | must run version verification on a Flutter-independent machine |

## NON-BLOCKING (do not gate implementation but need confirmation)
- D-7 reports scope confirmation (V1 = replicate analytics + CSV; advanced deferred).
- D-8 retention/privacy defaults confirmation (recommended defaults recorded).
- Notification centre S4 / Help S3 / Audit viewer S5 / Cloud backup on-or-off (each a PRODUCT-FREEZE governance approval, not a build blocker).
- 2FA (deferred).

## Rule
When a blocker is raised it is recorded here + in the affected task (Status=BLOCKED). An agent may not clear a BUSINESS DECISION itself; only explicit user/owner approval resolves it. Do not disguise a blocker as a task.
