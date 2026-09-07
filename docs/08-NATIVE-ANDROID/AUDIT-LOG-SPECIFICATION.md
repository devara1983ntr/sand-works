# Audit Log Specification

Status: Phase 0.5 (PROPOSED). Actor/timestamp/action/target/before/after/reason/source per privileged operation. Decides client-vs-trusted-backend generation.

## Decision: audit events are generated server-side (Cloud Functions), not client-side
Reason: client-generated audit can be forged/skipped by a modified client. Server/CF generates the audit record atomically with the operation it describes (same transaction where possible), so an operation that happened always has a truthful audit row. The client only displays audit logs (via a screen), never writes them.

## Event fields
| Field | Meaning | Notes |
|---|---|---|
| eventId | unique id (idempotent) | dedupe on retry |
| timestamp | server time | never client |
| actor | auth uid | resolved name for display only |
| actorRole | role at time | from server token/context |
| action | e.g. TRIP_CREATED, ROLE_CHANGED | enum |
| targetType/targetId | object mutated | collection + doc id |
| orgId | scope | |
| before | previous state snapshot (relevant fields) | for edits/corrections |
| after | new state snapshot | |
| reason | business reason (corrections, deletes) | where required |
| source | CF-trigger, schedule, manual | provenance |
| ip/client meta | optional, non-PII | security only |

## Operations always audited (server)
- Create/edit/delete of workSessions, trips.
- Attendance record & every correction (before/after + reason) — fixes reference losing history.
- Session close/open; status changes (owner + driver CF).
- Crew/driver/labourer add/edit/inactivate.
- User create, role change, status change (owner/CF) — the highest-risk.
- Announcements, backup/restore/export, restore rollback.
- Account data export/delete, sign-out-of-sensitive actions.
- Role/privilege-affecting and ownership fields.

## Privileged/data-integrity sensitive
Fields: role, ownerId, orgId, createdAt, status, approved — any change is audit-critical and server-enforced (see FIRESTORE-AUTHORIZATION-MATRIX).

## Access control
auditLogs readable by owner/admin only (rules), writable/deletable only by CF (client cannot). Audit log is append-mostly; no client deletion. Retention per D-8.

## Reading
Owner/Admin N-36 Audit viewer: filter by actor/action/date/type; shows before/after for edits; no PII beyond actor-name resolution in-org.

## Verification
PROPOSED; server-generates-audit is a backend requirement (BACKEND-COMPLETENESS) — flag to implementation. Test: each audited op yields exactly one correct audit row (integration); forge attempt (client writes auditLogs) rejected by rules.
