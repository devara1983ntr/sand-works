# Database Completeness

Status: Phase 0.5 (PROPOSED). Extends FIREBASE-DATABASE-DESIGN.md with per-feature data need, missing fields/entities, and per-entity state machines. No schema created.

## 1. Feature → data need chain (examples)
| Feature | Entity | Collection | Key fields | Gap |
|---|---|---|---|---|
| Record trip | Trip | workSessions/{id}/trips | tripNumber, driver, vehicle, status | status lifecycle undefined (D-6) |
| Attendance history | AttendanceRecord | trips/{id}/attendance + roll-up | status, recordedBy, correctedAt, reason | versioned history + correction reason missing (reference lost it) |
| Driver/labourer self-service (D-1) | driver/labourer uid link | users | linkedDriverId/labourerId | need user↔catalogue link |
| Reports | counters | workSessions (tripCount) + scheduled aggregates | totals | expensive if computed live → counters/CF |
| Notifications read/unread | NotificationItem | notifications/{userId} | read | unread index |
| Assignments (D-6) | assignedDriverId | trip | assignee, assignedBy, time | assignment/ack semantics |
| Backups | BackupRecord | (meta) | url, encrypted, version | cloud backup meta |
| Audit | AuditEvent | auditLogs | actor, action, prev/next | field-level before/after for corrections |

## 2. Missing fields/entities register
| Item | Missing now | Impact | Add (PROPOSED) |
|---|---|---|---|
| Attendance `recordedAt`/`correctedAt`/`correctionReason` | history lost | HIGH | versioned attendance |
| Trip real status enum + state timestamps | only string default | MED | D-6 lifecycle |
| labourerId/driverId user link | roles have no self view | MED (D-1) | users link fields |
| Org/owner scoping on every doc | reference single-user | HIGH | orgId + ownerId/createdBy everywhere |
| Soft-delete metadata | reference hard deletes | MED | deletedAt/deletedBy + audit |
| Session close status | none | MED | W6 close op |
| Report counter fields | none | MED | counters maintained by CF |
| Notification read/unread | none | MED | on NotificationItem |
| version/revision for optimistic concurrency | none | HIGH | `rev` on editable docs |
| Searchable snapshot (name denormalisation) | for labourer/driver display | MED | denormalised displayName with documented propagation |

## 3. Per-entity state machines (justified by product)
### WorkSession
```
Draft(open) → Open/Active → Closed (owner close, W6)
Closed → (owner correction allowed restricted window + audit; D-8) 
```
Transitions: who(owner), validation(unique open; date/session), mutation(status), notification(none), audit(close/correction), rollback(n/a — idempotent; close recorded).

### Trip
```
Planned → Accepted → InProgress → Completed   (driver legs D-6)
Planned → Cancelled
(if no driver self-service: owner records directly as Completed)
```
Who: owner; driver via CF for own (D-1/6). Preconditions per transition. Audit on every status change. Notification to owner (and driver on assignment). Conflicts guarded by rev + transaction.

### AttendanceRecord
```
Recorded(present/absent) → Corrected(owner, reason, audited)   [keep history]
Confirmed(driver/labourer, D-1) 
```
Who records=owner; who corrects=owner with reason; read by subject. Audit before/after.

### User account
```
Invited → Active → Suspended ⇄ Active  → Deactivated(owner/CF, guarded)
```
Who=owner/CF; guard ≥1 active owner; audit.

### Labourer/Driver catalogue
```
Active → Inactive (soft)   (owner; history preserved; not assignable when inactive)
```

## 4. Missing indexes / impossible-query checks
- labourer history query `labourerId+date` → need composite index; ensure query shape satisfies Security Rules (Rules are not post-query filters) — see FIRESTORE-AUTHORIZATION-MATRIX.
- "My assigned trips" for a driver → index org+driverId+status.
- read/unread notifications → index userId+read+createdAt.
- Flag: any query filtering by role on the client without server constraint = impossible under rules → design queries to be rule-compatible.

## 5. Rules conflicts (prospective)
- Writable protected fields (role, ownerId, createdAt) must be excluded from client writes — field-level security (see FIRESTORE-AUTHORIZATION-MATRIX).
- Deleting must be server-only (client cannot hard-delete org records).

## 6. Verification
Reference schema VERIFIED (DATABASE.md/DATA-MODEL); native schema PROPOSED; state machines only where product justifies; D-1/D-6/D-8 gate driver/status/lifecycle fields.
