# PRD2 — Operational & behavioural product specification

## 0. Purpose & relationship to PRD
PRD.md captures the product-level view. **PRD2.md** documents the deeper operational/behavioural specification: workflows, state transitions, validation, edge cases, failure/offline behaviour, data ownership, and audit requirements for the **current Flutter reference product**. Native-rebuild additions are `PROPOSED` and live mainly in `08-NATIVE-ANDROID/`.

- Status: VERIFIED (from code) unless labelled.
- Evidence: `lib/features/work/presentation/bloc/*`, screen files, `core/database/hive_setup.dart`, `settings_screen.dart`.

## 1. Global state machine (VERIFIED)
The UI is driven by BLoC states (`work_state.dart`, `history_state.dart`). Top-level states:
`WorkInitial → WorkLoading → (DashboardLoaded | WorkEmpty | WorkError | TripDetailsLoaded | WorkActionSuccess | NavigateToConfirmNextTripState)`.

History states: `HistoryInitial → HistoryLoading → (HistoryLoaded | HistoryEmpty | HistoryError)`.

### State transition table
| From | Trigger | To |
|---|---|---|
| Any | `LoadDashboardDataEvent(date, session)` | `WorkLoading` → `DashboardLoaded` / `WorkEmpty` (if no work & no trips) / `WorkError` |
| DashboardLoaded | `NavigateToConfirmNextTripEvent` | `NavigateToConfirmNextTripState` then re-emits the stored dashboard state |
| Any | `SaveNextTripEvent` / `SaveFullWorkTripEvent` | `WorkLoading` → `WorkActionSuccess` → reload dashboard |
| DashboardLoaded | `DeleteSpecificTripEvent`/`RemoveLatestTripEvent` | `WorkLoading` → reload dashboard |
| Any | `LoadTripDetailsEvent(tripId)` | `WorkLoading` → `TripDetailsLoaded` / `WorkError` |
| Dashboard/History | `LoadHistoryEvent` | `HistoryLoading` → `HistoryLoaded`/`HistoryEmpty`/`HistoryError` |

> Finding (VERIFIED): `AddQuickTripEvent` handler `_onAddQuickTrip` is **empty** — the comment states navigation is performed directly in the UI. Dead event + empty handler (see `06-QUALITY/BUG-DEFECT-REGISTER.md`).

## 2. Session & date rules (VERIFIED)
- Date string format `dd MMM yyyy` (`DateFormat('dd MMM yyyy')`).
- Session: hour `4 ≤ h < 12` → `Morning`, else `Evening` (`getCurrentSession`). NOTE: this differs from the root `PRD.md` claim ("Morning 00:00–11:59"); **code says 04:00–11:59**, i.e. 00:00–03:59 is Evening. Discrepancy to resolve (`BUSINESS-RULES.md`).
- Data is queried/grouped by literal `date` string and `session` string; there is no timezone handling beyond `DateTime.now()` local time.

## 3. Workflow: Add a new work + first/next trip
Two overlapping entry flows exist (VERIFIED):

**A. "Add Work" / "Add First Trip" (FAB & empty state)** → `push('/add-edit-work', {isNew:true})` → `AddEditWorkScreen`.
- If editing (from Details/History/Trip-details) it passes `editingTrip`, `editingWork`, `editingLabours`.
- On Save: requires ≥1 labour; creates/overwrites Work with deterministic id `work_<date-with-underscores>_<session>`; creates a new Trip with `tripNumber=0` (resolved to next number in BLoC); saves labour master records + TripLabour attendance. On success emits `WorkActionSuccess` → snackbar → `context.pop()` → dashboard reloads.

**B. "Next trip" quick add (plus button on Current-Trip card)** → `NavigateToConfirmNextTripEvent` → dashboard listener copies last trip's place/workType/labours → `push('/confirm-next-trip', {...})` → `ConfirmNextTripScreen`. On Save emits `SaveNextTripEvent` → `WorkActionSuccess` → snackbar → pop → reload.

> **Concurrency/`context.pop` finding (VERIFIED):** `ConfirmNextTripScreen._onSave` calls `context.pop()` immediately after adding the event, while the BLoC also reacts to `WorkActionSuccess`. `AddEditWorkScreen` relies on a `WorkActionSuccess` listener to pop. Because these are separate routes, double-pop is avoided only because each screen handles its own pop; but the ordering relies on BLoC listeners vs local pop and is fragile.

## 4. Workflow: Trip numbering (VERIFIED)
`CalculateNextTripNumberUseCase.call(date)`:
1. Fetch Morning work for date; if exists fetch its trips and compute max tripNumber.
2. Fetch Evening work for date; if exists compute max tripNumber.
3. Next = max(morning, evening) + 1.
Evidence: `work_usecases.dart`, tested by `test/unit/usecases/trip_numbering_test.dart`.

## 5. Workflow: Labour & attendance (VERIFIED)
- Labour master list lives in `labour_box` (unique id per labour; name persists as typed).
- Per-trip attendance in `trip_labour_box` keyed by composite `"<tripId>_<tripLabourId>"`.
- Add labour in trip-details: creates a `Labour` master + a `TripLabour` with `isPresent=true`, then reloads trip details.
- Edit name: `SaveLabourEvent` updates the master record; comment notes it does not reload trip details and just emits `WorkActionSuccess` — **master name edits may not refresh the open trip list** (behavioural risk).
- Presence toggle: `UpdateTripLabourEvent` writes `isPresent` and reloads.
- Remove: `DeleteTripLabourEvent` hard-deletes the row; snackbar with **Undo** that re-saves (`SaveTripLabourEvent`).
- In `AddEditWorkScreen` the labour rows are in-memory (`LabourFormModel`) until saved.

## 6. Workflow: Delete a trip (VERIFIED)
- Delete triggers cascade of `TripLabour` rows whose composite key starts with `"<tripId>_"` (prefix scan) then deletes the Trip (`work_local_data_source.dart deleteTrip`).
- History deletion also refreshes the history list after a 300 ms delay.

## 7. Backup / restore workflow (VERIFIED)
Backup:
1. `FilePicker.platform.saveFile` with suggested name `labour_backup_<timestamp>.labourbackup`.
2. Serialise all four boxes to JSON `{version, createdAt, app:"Labour Party", data:{works, trips, labours, tripLabours}}`.
3. Write to the user-selected path (SAF).

Restore:
1. `FilePicker.platform.pickFiles`; require `.labourbackup` extension.
2. Off-main-thread parse+validate via `compute(_parseAndValidateBackup, path)`: file ≤ 25 MB; valid JSON map; `app=="Labour Party"`; `version` & `data` present; expected top-level arrays non-null; row-count upper bounds (works≤10000, trips≤100000, labours≤5000).
3. Show summary dialog (backup date, size, counts) with explicit overwrite warning.
4. Snapshot current boxes → clear boxes → write restore records → verify counts match → success snackbar.
5. On any error: roll back from the snapshot and show an error dialog.

## 8. Failure / error / edge-case behaviour (VERIFIED, summary)
| Scenario | Behaviour | Evidence |
|---|---|---|
| Empty current work day | `WorkEmpty` → empty-state widget | `dashboard_screen.dart` |
| Repository/Db failure on load | `WorkError` with message text (no retry button on dashboard) | `dashboard_screen.dart` |
| Form missing work type/driver/labour | Inline validation + snackbar "At least one labour is required." | `add_edit_work_screen.dart` |
| `editingTrip` w/o `editingWork` | Throws `Exception` at initState | `add_edit_work_screen.dart` |
| Backup file invalid/too large/unsupported | Specific error dialogs | `settings_screen.dart` |
| Restore count mismatch / exception | Rollback from snapshot + error dialog | `settings_screen.dart` |
| No trips to show under a work | "No trips in current session" / "No trips found." | dashboard/details |
| History empty | Empty-state widget | `history_screen.dart` |
| Analytics empty | Empty-state widget | `analytics_screen.dart` |

> **Gap (VERIFIED):** Dashboard `WorkError` renders only the message centred with the error colour — **no retry affordance** and no "something went wrong" recovery path. Trip-details error likewise shows plain text. See `06-QUALITY/ERROR-STATES.md`.

## 9. Offline behaviour (VERIFIED)
The app is **intrinsically offline**: the only operations are local Hive reads/writes and file picker access. Main `AndroidManifest.xml` declares **no `<uses-permission>`**, including no `INTERNET`. There is no network call path to fail. Consequences:
- No permission requests are needed for core function (file export uses SAF scoped storage).
- There is no sync, no retry-of-network, no cache-of-remote concept. "Offline mode" == normal mode.

## 10. Data ownership (current vs proposed)
- **Current (VERIFIED):** All records are owned by the single local app user; no ownership metadata, no audit log, no multi-user separation.
- **Native (PROPOSED):** Introduce server-authoritative owner/admin; per-record `ownerId`, `createdBy`, timestamps; audit logs in Firestore; role enforcement server-side.

## 11. Audit requirements (current vs proposed)
- **Current:** none — there is no audit trail of who did what; the app has no login.
- **Proposed (native):** `changedAt`, `changedBy`, immutable create records, admin audit log collection, and server-side functions for privileged operations.

## 12. Synchronisation requirements (current vs proposed)
- **Current:** none (offline). Backup file is the only export.
- **Native (PROPOSED):** evaluate Firestore offline persistence + online merge with conflict policy; do not silently overwrite.

## 13. Notification requirements (current vs proposed)
- **Current:** none. No notification code, no FCM, no local notifications.
- **Native (PROPOSED):** evaluate FCM + Cloud Functions + Firestore for role-scoped announcements; document permission strategy in `08-NATIVE-ANDROID/ANDROID-PERMISSIONS.md`.

## 14. Validation rules register
| Field | Rule | Source |
|---|---|---|
| workType (add/edit form) | required | validator `v==null||v.isEmpty` |
| driver | required | validator |
| labours | ≥1 required to save | `_saveWork` guard |
| tractor | required in `ConfirmNextTrip` | validator |
| driver (ConfirmNextTrip) | required | validator |
| Backup extension | must end `.labourbackup` | `_parseAndValidateBackup` |
| Backup size | ≤ 25 MB | size check |
| Backup row counts | works≤10000, trips≤100000, labours≤5000 | parse |
| Work date/session | editing requires matching work to preserve isolation | initState throw |

## 15. Open operational questions
- OQ-4 Exact intended session boundary (code 04:00 vs PRD text 00:00). `UNVERIFIED`
- OQ-5 Whether trip deletion should be soft (history) or hard in future native. `UNVERIFIED`
- OQ-6 Required retention and audit depth for native. `UNVERIFIED`
