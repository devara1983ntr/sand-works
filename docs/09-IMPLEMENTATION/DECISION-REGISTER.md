# DECISION & BLOCKER REGISTER (implementation layer)

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
