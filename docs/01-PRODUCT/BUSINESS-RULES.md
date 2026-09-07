# Business Rules Register

Purpose: Every significant business/data rule with evidence and classification. Status legend per `00-AUDIT-INDEX.md`.

Status: VERIFIED unless labelled. Commit `2dd2fe4`.

## Rules
| BR-ID | Rule | Evidence | Status |
|---|---|---|---|
| BR-01 | A `Work` is uniquely scoped by `(date, session)`; dashboard queries the current date + session | `dashboard_screen.dart`; `getWorkByDateAndSession` | VERIFIED |
| BR-02 | A `Trip` belongs to exactly one `Work` (`workId`) | `trip.dart`, models | VERIFIED |
| BR-03 | A `TripLabour` belongs to exactly one `Trip` (`tripId`) and references one `Labour` (`labourId`) | `trip_labour.dart` | VERIFIED |
| BR-04 | Trip number: first=1; next = max(morning,evening)+1; edit preserves; delete does not renumber | `CalculateNextTripNumberUseCase`; tests | VERIFIED |
| BR-05 | New date or session resets numbering baseline (computed per date) | usecase(date) | VERIFIED |
| BR-06 | Labour `name` persists exactly as typed; must never auto-become `Labour N` | README/PRD; `_saveLabour` | VERIFIED |
| BR-07 | Deleting a `Trip` cascade-deletes its `TripLabour` rows (composite-key prefix) | `work_local_data_source.dart deleteTrip` | VERIFIED |
| BR-08 | Deleting/removing a labour from a trip must not delete the master `Labour` record | `DeleteTripLabourEvent` deletes join only; master separate | VERIFIED |
| BR-09 | Attendance (`isPresent`) stored per trip per labour | model | VERIFIED |
| BR-10 | Work id deterministic per `(date, session)`; saving repeatedly under same key aggregates trips to that Work | `_saveWork` `work_<date>_<session>` | VERIFIED |
| BR-11 | Editing an existing trip must preserve `id`, `tripNumber`, `createdAt`, `date`, `session` | `SaveFullWorkTripEvent` `_onSaveFullWorkTrip` preserves; tests `date_partition_test` | VERIFIED |
| BR-12 | Soft-delete semantics: labours removed from a full-trip save are marked `isPresent=false` (kept), not deleted | `_onSaveFullWorkTrip` | VERIFIED |
| BR-13 | Backup file validation: `.labourbackup`, valid JSON, `app=="Labour Party"`, version+data present, ≤25 MB, row bounds | `settings_screen.dart` | VERIFIED |
| BR-14 | Restore is all-or-nothing: snapshot → clear → apply → verify counts; rollback on failure | `settings_screen.dart` | VERIFIED |
| BR-15 | Session boundary in code = 04:00–11:59 Morning, else Evening | `date_time_utils.dart` | VERIFIED |
| BR-16 | (Doc claim) Session boundary "Morning 00:00–11:59" | Root `PRD.md` §36 | CONFLICT with BR-15 → resolve |
| BR-17 | Single user owns all records; no ownership metadata | codebase | VERIFIED |
| BR-18 | Trip `status` field exists, default `Completed`; no status workflow UI beyond read | model default; screens read-only | VERIFIED |

## Native rules — PROPOSED
| BR-ID | Rule |
|---|---|
| PBR-01 | Ownership: every record has `ownerId`; labourer reads only own attendance |
| PBR-02 | Role changes and privileged actions are server-enforced and audited |
| PBR-03 | Client role flags are display hints only, never authorization |
| PBR-04 | Trip/job status transitions restricted by role & current state |
| PBR-05 | Offline writes queue and reconcile without silent overwrites |

## Verification status
VERIFIED BR-01..BR-18 except BR-16 (open conflict). PROPOSED PBR-01..05.
