# Error State Matrix

Status: Phase 0.5 (PROPOSED). Complete error taxonomy with message/diagnostic/log/retry/nav/recovery/idempotency. Every screen maps its errors to these.

## Error taxonomy
| Error | User message (example) | Diagnostic | Logged? | Retry | Navigation | Recovery | Idempotent? |
|---|---|---|---|---|---|---|---|
| ValidationError | per-field message | reason | debug | re-submit | stay | fix field | yes (no write) |
| AuthenticationError | "Email or password incorrect" | auth code | aggregate | retry | stay on login | reset/recovery | yes |
| AuthorizationError/Forbidden | "You don't have permission" | role/rule denial | yes (no PII) | no (won't help) | to allowed area/Forbidden state | request access | no op |
| Unauthorized | "Please sign in" | no session | yes | re-auth | login | re-auth, preserve deep link | yes |
| NetworkError | "No connection" | reason | debug | yes | stay | reconnect | yes |
| TimeoutError | "Timed out" | latency | yes | yes | stay | retry idempotent | yes (idempotent) |
| NotFound | "This record no longer exists" | id/deleted | yes | no | back to list | refresh list | no |
| Conflict | "Saved by someone else" | rev mismatch | yes | manual merge | conflict UI | merge/reload | no |
| RateLimited | "Too many attempts, try later" | throttle | yes | backoff | stay | wait | yes |
| BackendError/CFError | "Server error, try again" | function error | yes | yes | stay | retry | depends |
| DatabaseError (local) | "Could not save locally" | exception | yes | yes | stay | retry; outbox safe | depends |
| StorageError | "Upload/download failed" | reason | yes | yes | stay | retry | yes (resumable) |
| NotificationError | "Couldn't send notification" | FCM result | yes | server retry | owner info | CF retry | yes |
| PermissionDenied (Android) | "Permission needed for X" | denial | no | guide | settings | request rationale | — |
| SessionExpired | "Session expired, sign in again" | token | yes | re-auth | N-08 | re-auth, preserve data | yes |
| UnknownError | "Something went wrong" + retry | catch-all (redact) | yes | yes | stay | retry | treat non-idempotent conservatively |

## Per-screen error coverage (summary)
- Login: AuthError, Network, RateLimited, Disabled, Offline, SessionExpired.
- Data lists (dashboard/work/reports/crew): Network, Offline(cache), Forbidden, NotFound(refresh), Backend, Empty handled separately.
- Edits/forms: Validation, Conflict, Network/offline(queued), Backend, SessionExpired(draft preserved).
- Attendance: Validation, PartialFailure(multi-row), Conflict, Network/queued.
- Backup/restore: Validation(file), Storage, Size, Rollback error (reference typed dialogs carried).
- Notification send: NotificationError surfaced to owner (server retry).
- Export/report: Storage, Timeout, RateLimited.

## Rules
- No bare "Something went wrong." without retry/recovery.
- Every retry is idempotent or guarded; typed errors feed diagnostics but never leak raw internals/secrets to the UI.
- Forbidden ≠ generic error; distinct state.
- Offline handled by OFFLINE-STATE-MATRIX, not as generic error.
- Verification PROPOSED; each error has a UI test.

## Gaps flagged
- Reference: dashboard/trip errors lacked retry (audit ERROR-STATES). Native adds Retry everywhere.
- Malformed server/Firestore response handling must map to BackendError (not crash).
