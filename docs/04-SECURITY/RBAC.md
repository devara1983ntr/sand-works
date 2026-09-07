# Role-Based Access Control (RBAC)

Status: current (VERIFIED: no roles) + proposed native matrix. Commit `2dd2fe4`.

## 1. Current reality (VERIFIED)
The current app has **no roles and no users**: it is a single local-user product. There is no Owner/Driver/Labourer distinction in code, no auth, no permission model. Roles exist **only as data fields**: a Trip stores a `driverName` string; a Labourer is a `Labour` entity + per-trip `isPresent`. There is no account-driven differentiation.

The brief's Owner is named **Ramesh Sahu**; that name is absent from the codebase. Representing the owner must be a **backend-authoritative** role in native, never hardcoded credentials.

## 2. Current capability table (VERIFIED)
| Capability | Current (single implicit app user) |
|---|---|
| View dashboard | Yes (S-02) |
| Manage users | No such concept |
| Assign jobs | N/A (trip data entry only) |
| View assigned jobs | View own trips only |
| Modify job/trip status | Edit trip details/work |
| Attendance | Toggle present/absent |
| Payments | No |
| Notifications | No |
| Reports | Light analytics only (S-05) |
| System settings | Backup/restore only (S-06); theme/about inert |
| Audit logs | None |

## 3. Proposed RBAC matrix (PROPOSED — native, server-enforced)
Legend: ✅ allowed; 🔒 owner only (server-gated); 🚫 denied; (—) not applicable. Enforcement = backend rules/Cloud Functions; UI simply hides items.

| Capability | Owner/Admin | Driver | Labourer/Worker |
|---|---|---|---|
| View dashboard | ✅ (full) | ✅ (driver home) | ✅ (own attendance) |
| Manage users (add/deactivate/suspend) | 🔒 | 🚫 | 🚫 |
| Assign jobs/works | 🔒 | 🚫 | 🚫 |
| View assigned jobs | ✅ (all) | ✅ (own) | 🚫 (attendance only) |
| Modify job status | ✅ | 🚫→(own trip status PROPOSED review) | 🚫 |
| Record/update attendance | 🔒/✅ | own confirmation only | view own |
| Payments/wages | 🔒 (owner) | 🚫 (view if provided) | view own if provided |
| Notifications send | 🔒 | receive own | receive own |
| Reports | 🔒 | 🚫 (limited own summary) | 🚫 |
| System settings/config | 🔒 | 🚫 | 🚫 |
| Audit logs | 🔒 | 🚫 | 🚫 |
| Profile management | ✅ own | ✅ own | ✅ own |
| Account/role management | 🔒 | 🚫 | 🚫 |
| Suspension/deactivation | 🔒 | 🚫 | 🚫 |
| Data export | 🔒 | 🚫 | own data request |
| Deep-link/notif handling | ✅ | ✅ own | ✅ own |

Notes:
- "Owner/Admin 🔒" means only an authenticated user whose server role is owner/admin may perform it.
- Any capability that a role may perform on its own records must still be scoped in rules by `ownerId`/membership (IDOR protection).
- Do not assume all roles have identical access; do not grant labourers/drivers admin abilities.

## 4. Enforcement model
- Auth UID → server role (custom claims / role doc).
- Firestore rules + Storage rules check `request.auth` role & ownership.
- Cloud Functions perform privileged ops & role assignment (admin-only).
- UI role flags = display/routing only.

See `AUTHENTICATION-AUTHORIZATION.md`, `THREAT-MODEL.md`, `08-NATIVE-ANDROID/FIREBASE-SECURITY-RULES.md`.
