# CHANGE CONTROL

Once implementation begins (post Gate-0), the roadmap and frozen scope become CONTROLLED. This defines how change is handled without silently altering requirements (AGENT §14).

## What is frozen/controlled
- PRODUCT-FREEZE scope (MUST/SHOULD; OUT/DEFERRED boundaries).
- FINAL specification set under `08-NATIVE-ANDROID/`.
- This control system (tasks, gates, dependencies, traceability).
- AGENT.md §14 (immutable; changes per §14.29).
- DEPENDENCY-POLICY.

## Change types
- New requirement / scope change / requirement reinterpretation / architectural deviation / dependency change / business-rule change.

## Mandatory change flow (no shortcuts)
```
NEW REQUIREMENT / CHANGE PROPOSAL
→ Impact analysis (product/scope/security/architecture/data/UX/offline/test)
→ Traceability update (TRACEABILITY-MATRIX + FINAL-TRACEABILITY)
→ Dependency update (DEPENDENCY-MATRIX)
→ Security review (SECURITY-ATTACK-REVIEW relevance; no new attack surface unexamined)
→ Test impact (FINAL-TEST-CONTRACT)
→ Task creation/modification (a task must never hide multiple unrelated changes)
→ APPROVAL (explicit user/owner sign-off; change is authorised and documented)
→ Implementation
```

## Rules
- An autonomous agent MUST NOT self-authorize a change that alters approved product behaviour, scope, security, or the Definition of Done.
- A "needed to get the build passing"/"temporary"/"replace later" rationale is never authorization (AGENT §14.29).
- Deferred/Out-of-scope items cannot be pulled in without approval; Required items cannot be silently dropped.
- Every approved change updates: this file (a change log row), affected task statuses, TRACEABILITY, RISK-REGISTER, and PROGRESS-TRACKER counts.

## Change log
| ID | Date | Change | Rationale | Approved by | Docs/tasks updated | Status |
|---|---|---|---|---|---|---|
| (none yet — implementation not started) | | | | | | |

## Ambiguity rule
If a requirement is ambiguous and impacts a critical path, raise it as a change/blocker (BLOCKED — DESIGN SPECIFICATION) rather than guessing.
