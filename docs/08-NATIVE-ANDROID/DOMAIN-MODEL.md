# Domain Model

Status: PROPOSED (Phase 0). Domain entities are **independent of Firebase/Room**. Model boundaries: Domain Model / Firebase Document Model / UI Model / DTO. Firestore documents must NOT become domain models directly.

## 1. Model boundary mapping
| Model | Purpose | Direction | Notes |
|---|---|---|---|
| Domain Model | business invariants, role-agnostic | core | no Android/Firebase imports |
| DTO / DataTransfer | wire/DB payload (Firestore doc, Room entity) | data layer | maps to/from domain |
| Firebase Document Model | Firestore fields | data/remote | mappers |
| Room Entity | local cache | data/local | mappers |
| UI Model / UiState | Compose presentation | presentation | built in ViewModel from domain/state |

Mapping is explicit via mappers in the data layer; **never pass Firestore `DocumentSnapshot`/`Map` into domain or UI**.

## 2. Domain entities
### Personas/role metadata
- `UserAccount { uid, orgId, role(Role), status(AccountStatus), displayName, phone?, email?, photoUrl?, linkedDriverId?, linkedLabourerId?, timestamps }`
- `Role` enum: OWNER, ADMIN, DRIVER, LABORER.
- `AccountStatus`: INVITED, ACTIVE, SUSPENDED, DELETED(deactivated).
- `Driver { id, orgId, name, phone?, vehicle?, uid?, active }`
- `Labourer { id, orgId, name, phone?, uid?, active }`

### Work/trip
- `WorkSession { id, orgId, date, session(Session), workType, place, status(OPEN/CLOSED), tripCount, timestamps }`
- `Session` enum: MORNING, EVENING (boundary value per D-5).
- `Trip { id, workSessionId, orgId, tripNumber, vehicleRef?, driverId?, driverNameSnapshot, status(TripStatus), notes?, place?, timestamps }`
- `TripStatus`: PLANNED, CONFIRMED, IN_PROGRESS, COMPLETED, CANCELLED (drive by D-6; keep minimal if no driver workflow).
- `AttendanceRecord { id, tripId, orgId, labourerId, status(PRESENT/ABSENT/CONFIRMED), recordedBy, timestamps }`

### Notifications
- `NotificationItem { id, userId, type, title, body, deepLink?, read, createdAt }`
- `NotificationPreferences { userId, enabledChannels }`

### Audit
- `AuditEvent { id, orgId, actorUid, action, targetType, targetId, previous?, next?, reason?, createdAt }`

### Settings/org
- `Organization { id, name, ownerUid, settings }`
- `BusinessSettings` (session boundary, defaults, reports).

## 3. Domain invariants (business rules) — from audit (BR register)
- A WorkSession is uniquely `(org, date, session)`.
- Trip belongs to exactly one WorkSession; tripNumber sequential per session/date (server-authoritative).
- Attendance belongs to one Trip + one Labourer; status transitions restricted by role/state.
- Labourer names persist as typed; driver/labourer are master-referenced (improved).
- Destructive delete is soft + audited; a closed session is immutable-ish.
- Role/status changes only via server (CF). (Extend in `BACKEND-CONTRACT.md`.)

## 4. What is deliberately NOT a domain entity
- Cloud function payloads, FCM message envelopes, Firestore internal metadata, UI-only flags (e.g., `isLoading`) — stay in their layers.

## 5. Ownership (feeds rules)
Record → owner: WorkSession/Trip/Attendance/Drivers/Labourers → org; UserAccount/profile → user (+ org admin for role/status); Notification → recipient; AuditLog → system; Settings → org.

## 6. Verification
PROPOSED. Names preserved from audited Flutter domain where the concept carries (Work→WorkSession, Labour→Labourer, Trip→Trip, attendance), with REPLACE/IMPROVE noted in PRODUCT-RECONCILIATION.
