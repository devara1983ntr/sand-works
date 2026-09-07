# Database (current — Hive)

Status: VERIFIED. Commit `2dd2fe4`.

## Engine
Hive (`hive` + `hive_flutter`). NoSQL key-value document store; boxes opened in `lib/core/database/hive_setup.dart`. Files live in the app-support directory resolved by `path_provider` via `Hive.initFlutter()`.

## Boxes
| Box constant | Generic type | Adapter typeId | Purpose | Primary key scheme |
|---|---|---|---|---|
| `work_box` | `WorkModel` | 0 | top-level works | work id (`work_<date_>_<session>` for created; uuid otherwise) |
| `trip_box` | `TripModel` | 1 | trips | trip id (uuid) |
| `labour_box` | `LabourModel` | 2 | labourer master records | labour id (uuid) |
| `trip_labour_box` | `TripLabourModel` | 3 | per-trip attendance joins | composite `"<tripId>_<tripLabourId>"` |
| `draft_box` | `DraftModel` | 4 | unsaved new-trip form | fixed key `'current_draft'` |

TypeId registry: WorkModel=0, TripModel=1, LabourModel=2, TripLabourModel=3, DraftModel=4 (Hive object versioning uses typeId + field index; fields must not be renumbered).

## Schema (fields) — see `DATA-MODEL.md` for detail
Each model maps 1:1 to its entity. `TripModel` fields carry Hive default values for forward/backward compat (`place=''`, `workType='Sand (Bali)'`, `notes=''`, `updatedAt=null`, `status='Completed'`).

## Keying / performance strategy (VERIFIED)
- `TripLabour` stored by **composite key** `"<tripId>_<id>"` to allow prefix scans: `getLaboursForTrip` filters keys starting `"<tripId>_"` (avoids full O(N) value scan); `deleteTrip` deletes the trip then prefix-deletes its TripLabour keys.
- Bulk writes use `putAll` (e.g., `saveTripLabours`) which deletes existing prefix keys first then inserts, to avoid orphans on edit.
- Legacy-key migration: on data-source construction, keys without `_` are copied to composite keys and removed.

## Integrity operations
- Cascade delete trip → attendance.
- Restore implements snapshot → clear → apply → verify → rollback.

## Findings (VERIFIED)
- No formal schema version/migration framework (only the bespoke legacy-key migration + Hive default values). Future model changes need careful field-index handling.
- No encryption at rest (Hive default). Labour names/phones stored in plaintext within app sandbox.
- Draft box stores JSON-encoded labour list in a string field (`encodedLabours`) — schema-in-string anti-pattern.
- Deterministic work-id by date+session means multiple "Add Work" saves on the same session reuse the same work key (aggregation) while still creating distinct trips — confirm intended semantics.

## Native notes
For native, evaluate **Room** (relational) where joins/cascade/queries are needed, or Firestore (see `08-NATIVE-ANDROID/FIREBASE-DATABASE.md`). Map each Hive box to a schema; version & migrate via Room migrations or Firestore structure.
