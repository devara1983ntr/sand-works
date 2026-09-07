# Data Model (current)

Status: VERIFIED. Commit `2dd2fe4`. Entities in `lib/features/work/domain/entities/`, models in `.../data/models/`.

## Entities & models
### Work (`work.dart` / `WorkModel` typeId 0)
| Field | Type | Required | Notes |
|---|---|---|---|
| id | String | yes | created: `work_<date spaces→_>_<session>`; else uuid |
| date | String | yes | `dd MMM yyyy` |
| session | String | yes | Morning/Evening |
| workType | String | yes | default seed `Sand (Bali)` |
| place | String | optional default '' | |
| createdAt | DateTime | yes | |
| updatedAt | DateTime | yes | |

### Trip (`trip.dart` / `TripModel` typeId 1)
| Field | Type | Required/default | Notes |
|---|---|---|---|
| id | String | yes | uuid |
| workId | String | yes | FK → Work |
| tripNumber | int | yes | derived; edit preserves; 0=auto on create |
| tractor | String | yes | e.g., Sonalika/JohnDeere |
| driverName | String | yes | |
| createdAt | DateTime | yes | |
| place | String | default '' | |
| workType | String | default 'Sand (Bali)' | |
| notes | String | default '' | |
| updatedAt | DateTime? | default null | |
| status | String | default 'Completed' | no status workflow UI |

### Labour (`labour.dart` / `LabourModel` typeId 2)
| Field | Type | Notes |
|---|---|---|
| id | String | uuid |
| name | String | persists exactly as typed |
| phoneOptional | String? | optional PII |
| createdAt | DateTime | |

### TripLabour (`trip_labour.dart` / `TripLabourModel` typeId 3)
| Field | Type | Notes |
|---|---|---|
| id | String | uuid |
| tripId | String | FK → Trip |
| labourId | String | FK → Labour |
| isPresent | bool | attendance |

### Draft (`draft_model.dart` typeId 4)
| Field | Type | Notes |
|---|---|---|
| date/session/workType/place/tractor/driverName | String | |
| encodedLabours | String | JSON string of `[{labourId,name,isPresent}]` |

## Relationships
```
Work 1 ── * Trip 1 ── * TripLabour * ── 1 Labour
Work * ── * Labour   (via TripLabour; Labour is a global master list)
```
Foreign keys are string references (no DB-level enforcement) — integrity enforced in repository/data source & usecase logic.

## Data integrity rules implemented
- Trip belongs to one Work; TripLabour to one Trip + one Labour.
- Delete trip → delete its TripLabour (prefix).
- Full-trip save marks removed labours `isPresent=false` (soft) rather than deleting (per PRD).
- Restore validates row counts and rolls back.

## PII inventory (VERIFIED)
- `Labour.name`, `Labour.phoneOptional` (optional phone) are personal data stored locally & exported unencrypted in backups. No other PII (no DOB, addresses, government IDs).
- Backup file also contains `createdAt` times and driver names.

## Findings / gaps (VERIFIED)
- Labour `phoneOptional` exists in the model but **no UI captures phone** (search/absence confirmed) — field unused by current UI. Confirm intent.
- Trip `status` fixed to default; no status state machine in UI.
- No ownership/audit fields (single-user by design).
- Duplicate definition of the presentation form model `LabourFormModel` in two screen files (not part of data model).
- `TripLabour.updatedAt` absent (no attendance-change timestamp) — attendance audit history not preserved (only latest state).

## Verification status
VERIFIED from models/entities. Future ownership/audit fields are PROPOSED.
