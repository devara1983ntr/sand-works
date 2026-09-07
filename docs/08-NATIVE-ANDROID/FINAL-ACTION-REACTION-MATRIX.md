# FINAL ACTION → REACTION MATRIX — V1 (Phase 0.75)

Every major user action with: Immediate feedback · Validation · Authorization · Loading · Backend · Database · Notification · UI update · Navigation · Failure · Recovery · Audit. Shared reaction engine; screen overrides noted.

## Common reactions (shared)
| Action | Feedback | Validation | Authz | Loading | Backend | Database | Notification | UI update | Navigation | Failure | Recovery | Audit |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Tap Login (N-03) | button spinner | email/format | Auth | Submitting | Auth | token | none | home | N-10/returnTo | typed err | retry/reset | auth(server) |
| Tap Save (editor N-24/25) | spinner+disable | inline+server | OWNER | Submitting | CF | write | none | list refresh | pop→origin | err/conflict/offline | retry(keep opId)/queue | AUD |
| Tap Save attendance (N-23) | spinner | reason-if-correct | OWNER | Submitting | CF | writes+history | none | counts | stay | partial/conflict | keep-saved/retry | AUD(before/after/reason) |
| Tap Delete (trip/labour) | confirm dialog | — | OWNER | confirm→Submitting | CF soft+cascade | soft-delete | none | row removed+undo | stay | err/conflict | retry/undo | AUD |
| Tap Close session (N-22) | confirm | state=open | OWNER | Submitting | CF txn | status+final | none | closed UI | stay | already-closed | reload | AUD |
| Tap Assign | N/A in V1 (deferred D-6) | — | — | — | — | — | — | — | — | — | — | — |
| Tap Accept/Start/Complete | N/A in V1 (D-6) | — | — | — | — | — | — | — | — | — | — | — |
| Tap Retry | spinner | — | per op | Loading | re-run op (same opId) | — | — | result | stay | err persists | retry/backoff | if op audited |
| Tap Notification (N-41) | none(foreground) | — | OWNER | — | mark-read; deep link | read | — | read state | target or NotFound-fallback | — | manual nav | — |
| Tap Logout (N-39) | confirm | — | OWNER | Submitting | sign-out | clear local cache/outbox (consent) | none | — | N-03 | err | retry | — |
| Pull-to-refresh | top spinner | — | OWNER | Refreshing | refetch | read | none | list | stay | offline note/err | retry | — |
| Swipe / long-press (row) | reveal | — | OWNER | — | — | — | — | action menu | — | — | dismiss | — |
| Back (all) | predictive | dirty→autosave | — | — | — | autosave draft | none | pop | previous | — | — | — |
| Tap Export CSV (N-27) | progress+cancel | range | OWNER | Progress | CF+Storage | export | completion | file actions | stay/result | err | retry | AUD |
| Tap Backup now (N-38) | progress | — | OWNER | Progress | CF | snapshot | complete/fail | last backup | stay | err | retry | AUD |
| Tap Restore (N-38) | confirm | version/verify | OWNER | Progress | CF | restore+rollback | complete/fail | data | stay | verify-fail→rollback | — | AUD |
| Tap Change password (N-39) | spinner | policy+current | OWNER | Submitting | Auth | — | none | success | stay | err | retry | auth |
| Tap Delete my data (N-39) | multi-confirm+re-auth | re-auth | OWNER | Submitting | CF | anonymize/delete | none | — | account state | guarded | — | AUD |

## Guarantees
- No destructive action is reachable without explicit confirm; reversible ones offer Undo.
- No action silently succeeds when offline: writes show queued/pending until server ack.
- Retry reuses opId; double-submit impossible while Submitting.
- Each row is a UI test + (where audited) an audit assertion (FT matrix).
