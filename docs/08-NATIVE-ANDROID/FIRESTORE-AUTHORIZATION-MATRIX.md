# Firestore Authorization Matrix

Status: Phase 0.5 (PROPOSED). For each collection/operation defines who may read/write with field-level security. Extends FIRESTORE SECURITY RULES DESIGN. Rules are NOT post-query filters — queries must be rule-valid.

## Roles
Owner (OWNER/ADMIN), Driver, Labourer, Authenticated-other, Unauthenticated.

## Collection × operation
Legend: R/W — who may; * server/CF-only (client cannot); P = project root.

| Collection | doc | create | update | delete | read | Owner/Admin | Driver | Labourer | Other-auth | Unauth |
|---|---|---|---|---|---|---|---|---|---|---|
| users | uid | *CF | self(fields limited)/*CF-role | *CF | self + owner(org) | full own | own | own | own | none |
| workSessions | session | owner | owner(open/close via CF) | *CF soft | org owner | yes(org) | own assign (D-1) | none | none | none |
| workSessions/{id}/trips | trip | owner | owner/driver-own(CF, D-6) | *CF soft | org owner | yes | own-assigned | none | none | none |
| trips/{id}/attendance | record | owner | owner; subject-read (D-1) | *CF soft | org owner + subject | yes | own? (D-1) | own (D-1) | none | none |
| labourers | labourer | owner | owner | *CF soft | org owner | yes | read ref | read own | none | none |
| drivers | driver | owner | owner | *CF soft | org owner | yes | read own | read ref | none | none |
| vehicles/tractors | veh | owner | owner | *CF soft | org owner | yes | read ref | read ref | none | none |
| notifications/{userId} | notif | *CF | self (read mark) | self/CF | self | own | own | own | own | none |
| auditLogs | event | *CF | *CF | *CF | owner/admin | yes | no | no | no | none |
| announcements | ann | owner/CF | owner/CF | owner/CF | owner + target | yes | if target | if target | no | none |
| reportExports (meta) | export | CF | CF | — | owner | yes | no | no | no | none |
| backups (meta) | backup | CF | — | — | owner | yes | no | no | no | none |

## Field-level security (writable-by) rules
| Field | Readable by | Writable by | Validation | Notes |
|---|---|---|---|---|
| role | self+owner | *CF only | enum | **never client** — block client role write (privilege escalation) |
| ownerId/orgId | all in org | *CF/server | — | immutable; never client |
| createdAt/createdBy | all in org | *server | server timestamp | never client |
| emailVerified/status | self+owner | *server | — | |
| workType/place/notes | org owner | owner | length/type | |
| tripNumber | org | *CF | sequential | server-authoritative |
| attendance.present | subject+owner | owner | bool | corrections need reason (owner) |
| audit metadata | owner/admin | *CF | — | |
| deletedAt/deletedBy | owner/admin | *CF | — | soft delete |
| displayName (denorm) | all in org | *CF + rename propagation | — | |

## Detected security problems (prospective rules design)
| # | Issue | Severity | Fix |
|---|---|---|---|
| A1 | Client-controlled role/status write | CRITICAL (escalation) | block client write; only CF |
| A2 | Cross-user read of other's attendance without scope | HIGH | owner scoped by org; subject read own only (D-1) |
| A3 | Any-role write to trips/attendance | HIGH | owner only; driver own via CF (D-6) |
| A4 | Client hard-delete of records (loss) | HIGH | soft-delete server-side only |
| A5 | Unbounded query by role w/o rule constraint | HIGH | queries must satisfy rules (not client-filtered) |
| A6 | Client tamper of createdAt/createdBy/ownerId | HIGH | server fields unwritable |
| A7 | Unauthorized audit log write | MED | *CF |
| A8 | Write protected-field data (approved/status) by client | MED | rule rejects |
| A9 | No ownership on labourer/vehicle refs | MED | scope by org |
| A10 | Subject (labourer) seeing others' records | MED (D-1) | query shaped by rules |

## Agreement requirement
- UI matrix (what each role sees/enables) must equal this backend/rule matrix (USER-ACTION / RBAC-DESIGN consistency). Rule tests in emulator cover every cell; escalation/ownership/delete tests explicit.
- Any mismatch between UI and rules = a gap recorded in PRODUCT-GAP-REGISTER.
- Rows gated by D-1 (driver/labourer self-service) and D-6 (driver workflow) are marked as such and remain DECISION-pending.
