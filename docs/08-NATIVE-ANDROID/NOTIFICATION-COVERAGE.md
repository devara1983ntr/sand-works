# Notification Coverage

Status: Phase 0.5 (PROPOSED). Extends NOTIFICATION-ARCHITECTURE. Every notification trigger/actor/recipient/priority/channel/payload/deep link/read state/failure/retry/expiration/audit.

## Event register
| Event | Trigger | Actor | Recipient | Priority | Channel | Payload | Deep link | Read | Failure | Retry | Expire | Audit |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Work assigned (D-6) | BO-6 | owner | driver | High | Work | tripId, title | N-51 trip | yes | FCM fail → CF retry/backoff; server status | yes idempotent | 24h | assign |
| Trip status update | BO-7 | driver | owner | Med | Work | tripId,status | N-23 | yes | retry | yes | 7d | status |
| Attendance confirmation (D-1) | labourer/owner | subject/owner | Low | Attendance | session, date | N-23/N-60 | yes | retry | yes | 7d | — |
| Announcement | BO-11 | owner | topic audience | Med | Announce | title,body | N-35/N-41 | yes | retry | yes | 30d | announce |
| Session closed | BO-5 | owner/CF | owner | Low | System | session | N-22 | yes | retry | yes | 7d | close |
| Backup complete/fail | BO-12 | CF | owner | Low | System | state | N-38 | yes | retry | yes | 7d | backup |
| Restore complete/fail | BO-13 | CF | owner | High | System | state | N-38 | yes | retry | — (one-shot) | 7d | restore |
| Export ready | BO-14 | CF | owner | Low | System | url | N-28 | yes | retry | yes | 7d | export |
| Account role/status changed | BO-10 | owner/CF | affected user | Med | Security | newRole | N-34/N-39 | yes | retry | yes | 7d | role change |
| Session expiry/security alert | Auth/CF | system | user | High | Security | reason | N-08 | yes | n/a | n/a | 24h | auth event |
| Daily reminder (optional, D-8) | Scheduler | CF | owner/driver | Low | Reminder | pending count | N-20/N-50 | yes | retry | yes | 24h | — |
| New work session (owner has none) reminder | — | CF | owner | Low | Reminder | — | N-20 | yes | — | — | 24h | — |

## Channel model (notification channels)
Work, Attendance, Announcements, Security, System, Reminders — each user-toggleable; Permission POST_NOTIFICATIONS handled per ANDROID-PERMISSIONS (rationale + settings; never broad).

## Deep links
Target screens are reachable only with valid auth + role + ownership; on failure show friendly NotFound/Forbidden with a fallback list link (see NAVIGATION).

## Read state
Per-user NotificationItem unread; read on open; mark-read offline semantics = DECISION (queue or on-read-online). Unread count badge in More/nav.

## Rules
- Data notification payloads avoid sensitive detail for privacy; tap reveals (D-8/privacy).
- Server-generated notifications (CF) with idempotency to prevent duplicates (see CONCURRENCY-AUDIT).
- FCM token/topic lifecycle: refresh on role change; remove on suspend/delete to prevent stale delivery.
- No silent/misleading "data changed" notifications without content path.

## Gaps flagged
- All notification events are PROPOSED; the earlier requirement list referenced notifications but events above are defined now. Confirm event set w/ D-7/D-8.
- Driver/labourer-specific notifications gated D-1/D-6.
- Verification: event set + a retry/failure/deep-link test each.
