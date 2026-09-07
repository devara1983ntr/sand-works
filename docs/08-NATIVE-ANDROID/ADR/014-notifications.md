# ADR-014: Notification architecture

## Context
Flutter has no notifications (VERIFIED). Product may need role-scoped assignment/attendance/admin notifications with read/unread and deep links.

## Decision
Use **FCM** driven by **Cloud Functions** (never client broadcast). Server resolves recipients (role/org/personal topics or token lists via CF), stores `notifications/{userId}` history, and client marks read + deep-links with re-authorization. Token registration/refresh/revocation managed to the user's own doc. Enabled in a later v1.x once backend & POST_NOTIFICATIONS are accepted (not v1.0 core if scope trimmed).

## Why chosen
- Server-authoritative sends; recipients scoped server-side.
- Payload never grants access (rules still govern).
- Matches role-scoped requirements.

## Alternatives
- Local-only notifications: insufficient for cross-role assignment.
- Client direct send: insecure.

## Trade-offs / consequences
- Requires CF + token lifecycle + permission strategy (ANDROID-PERMISSIONS.md).
- Foreground/background/killed handling; read/unread retention.
- Not a v1.0 hard dependency if product trims notifications (ADR-014 staged).
