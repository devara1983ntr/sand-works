# NOTIFICATION & ALERT SPEC — SAND WORKS

Targeted by role/user. No broadcast of private financial info to unrelated users. CF sends (server-side); client never broadcasts.

## Notification types (A–F)
| ID | Trigger | Recipients | Deep link | Channel/priority |
|---|---|---|---|---|
| A | Daily earnings summary (post-closure) | eligible users | Labourer/Driver Home | Earnings (default) |
| B | Owner warning | recipients list (owner chooses) | Alert UI | Alerts (high) |
| C | Approval result (approved/rejected) | the driver/labourer | Their Home/Profile | System |
| D | New assignment (temporary labour elevated role) | assigned labourer | Assignment | Work (high) |
| E | Assignment expiration | assigned labourer (+ notify owner) | Assignment/Home | Work |
| F | Relevant operational events (e.g., new trip affecting you, status change) | affected user | Trip/Home | Work |

## Rules
- Recipient is always the specific affected user(s) determined server-side; never a raw broadcast of another user's money.
- Money amounts in notifications are the **user's own** accrued summary, not others', and use locked wording ("earnings added"/"summary") — never "payment completed".
- Read state per-user; offline read handled per OFFLINE-SYNC.
- Channels user-toggleable where appropriate; POST_NOTIFICATIONS rationale+settings; no broad-request abuse.

## Owner alert/warning (B) — honest Android limits (§17)
- Purpose: emergency-style operational warning to drivers/labourers/approved users when owner needs immediate attention. Optional message, e.g., "Everyone report to the work location immediately."
- Implementation = **strongest compliant alert**:
  - high-priority channel, vibration pattern, custom alert sound, heads-up where the system permits.
  - prominent alert UI + acknowledgement/dismissal; records sender, timestamp, recipient list.
- **Explicit Android limitations (never claim otherwise):**
  - App MUST NOT claim it can force full-volume sound when the device is in silent mode or system-level DND. Android controls notification behaviour.
  - If sound is blocked by policy, use vibration/visual alert where allowed.
  - Never attempt unsafe/deceptive volume manipulation.
  - Do not misuse full-screen intents (restricted to appropriately urgent use cases on modern Android); use a compliant in-app prominent alert screen + normal high-priority notification instead.
- Acknowledgement persisted (recipient, time) so owner sees who has confirmed (audit/operational).

## Messaging
Only OWNER can send messages through the app (in-app operational messaging, gated to owner). Drivers/labourers cannot send owner messages.

## Tests
Notification targeting correctness (each type → correct recipients only), no private-financial cross-broadcast, read state, deep-link auth, alert ack, expiry/reassignment notifications, offline read, channel/preference config, no fake send (server retry).
