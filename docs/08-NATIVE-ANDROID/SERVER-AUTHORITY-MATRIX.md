# SERVER-AUTHORITY MATRIX — V1 (Phase 0.75)

For every value that must NOT be trusted from the Android client. Columns: Field · Client may provide on create? · Server generates? · Server validates? · Client may modify after create? · Who may modify · Rule/CF enforcement.

## Identity / security
| Field | Client provide | Server generates | Server validates | Client modify | Who may modify | Enforcement |
|---|---|---|---|---|---|---|
| uid | No | Yes (Auth) | Yes | No | Auth/CF | Auth |
| role | No | No (set at provisioning) | Yes | **No** | CF only | rules deny write; CF sets |
| status (active/suspended) | No | No | Yes | **No** | CF only | rules deny |
| orgId / ownerUid | No | Yes (provisioning) | Yes | **No** | CF | immutable, rules deny write |
| createdBy | No | Yes (request.auth.uid) | Yes | No | server | rules set/deny |
| changedBy | No | Yes (uid) | Yes | No | server | rules |
| createdAt/updatedAt | No | Yes (server timestamp) | Yes | **No** | server | FieldValue.serverTimestamp; rules deny literal |
| emailVerified | No | Yes | Yes | No | Auth | rules |
| driverId/labourerId link (uid) | On profile only w/ CF | — | Yes | No | CF | V2 (D-1) |

## Business / sequencing
| Field | Client provide | Server generates | Server validates | Client modify | Who | Enforcement |
|---|---|---|---|---|---|---|
| tripNumber | No | Yes (CF sequential) | Yes | No | CF | R-10 transaction |
| WorkSession uniqueness (date,session) | provides values | — | Yes (unique) | No(dup) | CF | R-01 transaction |
| Session status (open/closed) | open on create | — | Yes | No direct | OWNER via CF | rules |
| isPresent derived | provides status | derive isPresent | Yes | via correction | OWNER | CF |
| recordedBy/changedBy | No | Yes | Yes | No | server | rules |
| counters (tripCount/present) | No | Yes (increment/CF) | Yes | No | CF | FieldValue.increment |
| rev | reads | Yes | Yes | writes must match | OWNER edit | optimistic concurrency |
| soft-delete deletedAt/by | No | Yes | Yes | No | CF (owner request) | rules deny client delete |
| correction reason | Yes (required) | — | Yes (non-empty) | No | OWNER | CF |
| approval/confirmed flags | No | No | Yes | No | CF | V2 (D-1) reserved |

## Data content (client-authoritative but validated)
Client MAY provide (validated server-side, not trusted blindly): labourer/driver name, phone (format), place, notes, workType, workType default, session selection value (within enum), attendance status per labourer (must exist in roster), photo upload (type/size), settings values (enum/range).
Server still validates type/length/enum/roster membership.

## Admin/operator-derived flags
| Field | Client | Server | Who | Notes |
|---|---|---|---|---|
| notification read | Yes (self) | validate self | recipient | owner V1 |
| notification sends | No | Yes | CF | never client broadcast |
| audit log rows | **No** | Yes | CF | rules deny client write/delete |
| backup signature/encrypt key material | No | Yes | CF/storage | never client secrets |
| export/backup metadata | No | Yes | CF | |

## Principles
- Server generates and is sole authority for: identity, role, org, timestamps, audit actor, numbers, status transitions, deletion, guards, notifications, backup integrity.
- Client may only ever provide domain content values (validated) and its own `opId`.
- A client-provided privileged value (role, ownerId, status, isAdmin, permission list) is **always ignored/rejected**.
- Enforcement triple: UI hides what role can't do; Firestore/Storage rules deny; CF performs privileged op. (Rule-of-three, RBAC-Design §3.)
