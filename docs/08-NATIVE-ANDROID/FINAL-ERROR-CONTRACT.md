# FINAL ERROR CONTRACT — V1 (Phase 0.75)

Standardized handling for every error class. User message · Recovery · Retry · Logging · Analytics (if appropriate) · Navigation. Shared component `ErrorUi`/`ErrorState` renders all; screens don't invent patterns. No bare "Something went wrong" without retry/recovery; typed errors never leak raw internals/PII.

| Class | User message (example) | Recovery | Retry | Logging | Analytics | Navigation |
|---|---|---|---|---|---|---|
| Validation | per-field message | fix field | re-submit (no write) | debug | — | stay |
| Authentication | "Email or password is incorrect" | retry / reset | yes (idempotent) | aggregate only | login_failure | stay on login |
| Authorization/Forbidden | "You don't have permission for this" | request access / go allowed | no (won't help) | yes (no PII) | error_code | to allowed area |
| Unauthorized | "Please sign in" | sign in | — | yes | — | N-03/N-08 (preserve deep link) |
| Not Found | "This record no longer exists" | reload list | no | yes | error_code | back to list |
| Conflict | "Changed by someone else" | choose keep/reload | manual merge | yes | conflict | conflict UI |
| Network | "No connection" | reconnect | yes | debug | — | stay (offline banner) |
| Timeout | "Timed out, try again" | retry | yes | yes | — | stay |
| Offline | per offline contract | reconnect/sync | on reconnect | debug | — | stay + banner |
| Firebase/Firestore | "Sync error" | retry / re-sync | yes (idempotent) | yes | — | stay; pending visible |
| Storage | "Upload/download failed" | retry | yes (resumable) | yes | — | stay |
| Notification send | "Couldn't send notification" | server retry | server | yes | — | owner info |
| Rate Limited | "Too many attempts, wait" | backoff | after wait | yes | — | stay |
| Server (CF) | "Server error, try again" | retry | yes (idempotent) | yes | — | stay |
| Session expired | "Session expired — sign in again" | re-auth | — | yes | — | N-08 (preserve outbox/draft) |
| Permission denied (Android) | "Needs permission for X" | grant in settings | after grant | no | permission event | settings/photo-picker |
| Unknown | "Something went wrong" | retry | conservative (non-idempotent guarded) | yes (redact) | error_code | stay |

## Rules
- Retry always idempotent or guarded (opId). 
- Forbidden distinct from generic error and from Unauthorized.
- Offline is NOT an error state — OFFLINE contract owns it.
- Every error has a UI test; typed errors mapped to a ViewModel sealed error + log tag.

## Offline banner vs error
See FINAL-UI-STATE-CONTRACT. Errors thrown while offline are queued, not shown as failure.

## Verification
Global error handling in ANDROID-ERROR-HANDLING at app scope; FT-ERR covers each class.
