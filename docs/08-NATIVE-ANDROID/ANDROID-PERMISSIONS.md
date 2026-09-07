# Android Permissions (native)

Status: PROPOSED. Principle: **never request permissions preemptively**; request contextually when the feature needs them; document denial behaviour and settings recovery. (Current Flutter app declares no permissions — VERIFIED; native adds only as features require.)

## Register
| Permission | Feature | When requested | User explanation | Denial | Permanent denial | Settings recovery | Security note |
|---|---|---|---|---|---|---|---|
| INTERNET | Firebase Auth/Firestore/FCM/Storage | normal capability (not a runtime prompt) | — | — | — | — | required once backend added |
| POST_NOTIFICATIONS (13+) | FCM push (assignment/announcements) | when user enables notifications (contextual) | "Get alerts for assigned work/attendance" | in-app notification centre still works via Firestore | guide to settings | yes (deep link) | notification payload is never authorization |
| CAMERA / photo access | capture trip/site/profile photos (PROPOSED; D-8) | on first capture | explain | manual image input | settings | use photo-picker where no permission | avoid broad storage |
| Location (fine/coarse) | driver trip GPS tracking | **NOT required in v1** — DEFER unless product confirms | — | — | — | — | only add if justified; prefer manual status |
| Storage/media | backup export/import | none needed (use SAF) | — | — | — | — | avoid broad storage |
| Foreground service/exact alarm | scheduled reminders / background sync | only if needed | — | — | — | — | optional |

## Rules
- Contextual + rationale; minimal; each added permission documented here with denial/permanent-denial/settings-recovery.
- Use SAF and the Android Photo Picker to avoid storage permissions entirely where possible.
- Never request location/CAMERA unless a confirmed product feature needs it (D-1/D-8).

## Verification
PROPOSED. Current app = no permissions (VERIFIED). Native list above.
