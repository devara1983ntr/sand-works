# ROLE & USER MODEL — SAND WORKS

Locked roles: OWNER · DRIVER · LABOURER. ONE owner. No admin/delegated role. All roles are authenticated users. Authorization is backend-authoritative (Security Rules + Cloud Functions); UI hiding is never authorization.

## Capability summary
| Capability | OWNER | DRIVER | LABOURER |
|---|---|---|---|
| Sign in | ✓ | ✓ | ✓ |
| View/approve user registration | ✓ | ✗ | ✗ |
| Manage drivers/labourers/tractors | ✓ | ✗ | ✗ |
| Manage rates & money rules | ✓ | ✗ | ✗ |
| Approve users / role status | ✓ | ✗ | ✗ |
| Create/edit trips | ✓ | ✓ (permitted own trips) | ✗ |
| Select tractor & labourers for a trip | ✓ | ✓ | ✗ |
| View assigned/own operational info | ✓ | ✓ (own scope) | own attendance/history |
| Add trip (driver) | — | ✓ | ✗ |
| View own totals & share via WhatsApp | — | ✓ | ✗ |
| View profile/stats/earnings/leaderboards | ✓ | ✓(own) | ✓(own; read-only) |
| Modify attendance/money | ✓ (corrections w/ reason+audit) | ✗ | ✗ |
| Send operational warning / owner alert | ✓ (owner only) | ✗ | ✗ |
| Send messages in-app | ✓ (owner only) | ✗ | ✗ |
| Export data (PDF/CSV) | ✓ | ✗ | ✗ |
| Configure system settings | ✓ | ✗ | ✗ |
| Assign labourer temporarily | ✓ | ✓ (authorised, limited) | ✗ |
| View leaderboards | ✓ | ✓ | ✓ |

## User approval
- New DRIVER registration → OWNER approval.
- New LABOURER registration → OWNER approval.
- No newly registered operational user gets privileged access until approved.
- Approval state is backend-authoritative (`users.status`/`approval`); never client-writable; never rely on UI hiding.
- (Confirm) Self-registration enabled vs owner-created accounts — directive implies self-registration requiring approval.

## Temporary labour assignment (elevated operational role)
- If the normal driver is absent, OWNER (or an authorised DRIVER per SW-7) may assign a specific LABOURER to perform an operational role for a limited period.
- Required fields: labourerId, assignedBy, startDateTime, endDateTime, reason, scope, status, createdAt, expiresAt.
- OWNER customises the duration (endDateTime).
- **Expiry enforced by backend**: when expired, `access` to elevated scope automatically denied by Security Rules/CF; labourer cannot continue.
- No permanent privilege escalation. Never escalate to OWNER/admin (there is none). Elevated scope = driver-trip operations only, time-boxed.

## Account lifecycle states
- users: `status ∈ {invited, active, suspended}` + `approval ∈ {pending, approved, rejected}` for driver/labourer; owner is active/approved by provisioning.
- Suspension/approval/role/status changes are OWNER-only via CF; audit each.

## Scoping
- Single org (family). Every business record carries `orgId`; every query and rule constrains by org + role + ownership (`driverId`, `assignedDriverId`, `labourerId` subject scope). No cross-org/cross-user reads.
- A driver must never receive owner-only capabilities; a labourer must never write.
