# TASK MANAGEMENT

## Status vocabulary
BLOCKED (missing prerequisite/decision/env/asset) · READY (all prerequisites satisfied, not started) · IN PROGRESS · COMPLETE (evidence-based) · DEFERRED (explicit approval; not built) · CANCELLED (decision).

## Task types
foundation · domain · data · backend · security · ui · testing · release · asset · infra.

## Task contract
Every task in `tasks/PHASE-*.tasks.md` follows `tasks/TASK-CONTRACT-TEMPLATE.md`. Missing field on a critical path → BLOCKED — DESIGN SPECIFICATION.

## Small-step execution model (mandatory loop)
```
READ → UNDERSTAND → VERIFY PREREQUISITES → SELECT NEXT UNBLOCKED TASK
→ IMPLEMENT → TEST → STATIC ANALYSIS → SECURITY CHECK → REVIEW DIFF
→ UPDATE TASK STATUS → UPDATE TRACEABILITY → UPDATE PROGRESS → SELECT NEXT
```
Never jump randomly between unrelated phases. Never execute a task whose prerequisites are incomplete.

## Sub-task rule
Each screen task in Phase 4/5/6 carries a mandatory 8-part sub-checklist (state contract / ViewModel / repo-data source / UI / states / accessibility / responsive / tests). The agent may split these into discrete commits but must not skip any part. Do not create tasks that hide multiple unrelated features; split unless architecturally coupled (§24).

## Blocker protocol (AGENT §14.23)
If a task cannot be completed truthfully:
- Set Status=BLOCKED.
- Record in `DECISION-REGISTER.md` and this file's blocker log: Blocker · Cause · Evidence · Affected tasks · Required decision/input · Temporary action (if any).
- Do NOT fabricate / stub / silently skip / mark complete / create fake data / weaken requirements.
Classify: BLOCKED — BUSINESS DECISION / DESIGN SPECIFICATION / MISSING ASSET / BACKEND CONTRACT / CREDENTIAL-ENVIRONMENT / EXTERNAL DEPENDENCY / SECURITY REQUIREMENT.

## Selecting the next task
Pick the lowest-ID READY task whose hard prerequisites are COMPLETE and which lies on the current phase's critical path or an approved parallel group (DEPENDENCY-MATRIX).

## No self-authorized exceptions
An agent may not grant itself an exception to §14 or to any rule to "keep moving". Prefer a truthful blocker over fabricated progress (AGENT §14.29).

## Blocker log
| Task | Status | Blocker | Cause | Required input |
|---|---|---|---|---|
| IMPL-101 | BLOCKED | D-4 identity | business decision | confirm applicationId/namespace/signing |
| IMPL-103,301 | BLOCKED | credentials/env | no Firebase project/keys | provide project + google-services (never commit) |
| IMPL-704 | BLOCKED(if asset absent) | MISSING ASSET | no authoritative logo/brand | provide authoritative brand asset |
| Gate-0 | BLOCKED | scope S-V1 + D-3/D-4 | business decision | owner sign-off + decisions |
