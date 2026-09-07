# Conditional Logic Matrix

Status: Phase 0.5 (PROPOSED). Every important condition defines True vs False behaviour across UI/Backend/Nav/Security/Recovery. This is central to completeness — conditions with only one branch are flagged.

Legend: True→… / False→…; "reactive change" = what happens if the condition flips while on screen.

| # | Condition | TRUE → behaviour | FALSE → behaviour | Reactive change on screen | Security |
|---|---|---|---|---|---|
| C1 | Authenticated? | → role route/home | → Login (N-03); preserve deep link | session-expiry → N-08 re-auth | rules require auth |
| C2 | Role == OWNER/ADMIN? | owner nav/actions | driver/labourer home or Forbidden | role revoked mid-session → Forbidden/relog | backend role (never client) |
| C3 | Role == DRIVER (self-service)? | driver home, own data | n/a (else Forbidden) | — | scope by uid |
| C4 | Role == LABORER? | own attendance | n/a | — | scope by uid |
| C5 | Work assigned? | shows assigned driver/labour | unassigned (assignable by owner) | assignment change → live update | rules check assignee |
| C6 | Work status (planned→…→completed)? | progress actions per state | terminal (no further edits; owner override audited) | state change on concurrent edit → conflict | transitions validated server-side |
| C7 | Work cancelled? | read-only + reason | normal flow | — | cancel audited, owner |
| C8 | Data exists (list/query)? | show data | Empty state (per EMPTY matrix) | appears/disappears → empty↔content | — |
| C9 | Network online? | sync allowed; live | Offline banner; reads cache; writes queued | reconnect → auto-sync queued ops | queue idempotent |
| C10 | Permission granted (POST_NOTIFICATIONS etc.)? | enable channel | in-app centre still works; prompt rationale | toggle → re-prompt/settings | no broad request |
| C11 | Notifications enabled? | subscribe FCM topic | still reads notification centre | — | server scope |
| C12 | Session expired? | re-auth, keep drafts/outbox | normal | token refresh | re-auth gating |
| C13 | Form dirty? | warn/autosave on back/leave | allow nav | lifecycle pause autosaves | — |
| C14 | Validation fails? | inline errors, block submit | submit | — | server re-validates |
| C15 | Owner editing completed work? | restricted window + audit | blocked | — | audit |
| C16 | User suspended/deleted? | block/disable (N-06) | normal | mid-session disable → sign-out message | server status check |
| C17 | Assignee/creator == current uid? | allow scoped op | Forbidden | — | ownership rule |
| C18 | Duplicate session (date+session exists)? | reject/merge UX | allow create | — | uniqueness via CF |

## Missing false-path behaviour identified
| Condition | Gap |
|---|---|
| C5 assignment | No explicit "unassign/reshuffle while assigned driver offline" flow → CONFLICT/requeue behaviour needed (PROPOSED) |
| C9 network mid-submit | Edge: network drops after server success before client ack → idempotency needed (EDGE-CASE-AUDIT) |
| C12 expiry during an offline queue flush | Must not drop outbox; reconcile after re-auth |

## Verification
Conditions are PROPOSED from reconciled workflows; each must be mirrored in Firestore rules (C1..C4,C16,C17 authority) and CF state machines (C5..C7,C15,C18). Emulator tests cover each true/false branch.
