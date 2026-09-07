# Notification Architecture (FCM)

Status: PROPOSED. Never treat notification payloads as authorization — data access is still governed by rules.

## 1. Flow
```
Server/CF event (job assigned, attendance recorded, admin notice)
   → CF resolves recipient topics/UIDS (role/org-scoped, no client broadcast)
   → FCM push with data payload {type, deepLink}
   → Client receives (foreground/background)
   → Store NotificationItem (cloud) + update read/unread (self)
   → Optionally open deep link (navigation to the scoped screen)
```

## 2. Token registration/refresh/revocation
- Client registers FCM token to its own `users/{uid}/fcmTokens` on login & on `onNewToken`.
- Token refresh handled by FCM SDK; remove stale tokens on push failure (CF).
- Revoked token / sign-out → remove token from the user's doc (no cross-user reuse).
- App Check on all token writes.

## 3. Topics/subscriptions vs direct UID
- Role/org notifications: subscribe a device to `org_{orgId}` and/or role topic only via server registration rules; never let a client arbitrarily subscribe to another's topic.
- Personal notifications: direct to `users/{uid}` token list (server).
- Never trust a client-supplied recipient list.

## 4. Notification types (role-scoped)
| Type | Audience | Trigger | Deep link |
|---|---|---|---|
| WORK_ASSIGNED | driver (if D-1/6) / labourer | owner assigns | trip/work detail (scoped) |
| STATUS_UPDATED | owner + relevant | status change | detail |
| ATTENDANCE_CONFIRMED | labourer (if self) | confirm | attendance |
| ADMIN_ANNOUNCEMENT | org members | owner | notice |
| SYSTEM (backup/export ready) | owner | CF job | settings |

## 5. Read/unread & history
- `notifications/{userId}/{id}` server-created; client marks read; history list with unread count; retention window per policy.
- Cleanup of old notifications by CF on schedule.

## 6. Foreground vs background
- Foreground: show in-app notification center + optional heads-up; no duplicate push if in-app.
- Background/killed: FCM data + notification; tapping deep-links into the scoped screen with auth check.

## 7. Permission strategy
POST_NOTIFICATIONS (Android 13+) requested contextually when enabling notifications; denial handled gracefully (in-app centre still works via Firestore reads); permanent-denial → settings deep link. Never request preemptively. See `ANDROID-PERMISSIONS.md`.

## 8. Monitoring
Track FCM delivery (CF analytics on send result), token errors, notification open rate (opt-in). No unnecessary PII.

## 9. Anti-patterns
- No client-side broadcast; no trusting payload to grant access; no storage of tokens outside secured user docs; no logging of notification content secrets.

## 10. Verification
PROPOSED; enabled in a later v1.x (ADR-014). Requires POST_NOTIFICATIONS + backend before acceptance.
