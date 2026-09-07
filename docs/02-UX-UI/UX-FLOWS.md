# UX Flows

Purpose: Detailed flow diagrams for the current product. Each flow lists user action → application response → state transition → (local data) → next navigation. Status: VERIFIED (code-traced) unless PROPOSED. Commit `2dd2fe4`.

Legend: `DB` = Hive local; `/route` = go_router.

## 1. First launch / session start
```mermaid
flowchart LR
  A[Launch] --> B[main: init Hive + DI]
  B --> C[/splash SplashScreen/]
  C -->|1500 ms| D[/dashboard DashboardScreen/]
  D -->|LoadDashboardDataEvent today/session| E[WorkLoading skeleton]
  E -->|no records| F[WorkEmpty empty-state]
  E -->|records| G[DashboardLoaded summary+trips]
  E -->|db failure| H[WorkError text]
```
User action: none (auto). State: WorkInitial→Loading→Loaded/Empty/Error.

## 2. Add a new Work+Trip
```mermaid
flowchart LR
  A[Tap Add Work FAB] --> B["push('/add-edit-work', {isNew:true})"]
  B --> C[Fill work type, driver, add labour]
  C --> D[Save Trip]
  D -->|form valid & >=1 labour| E[SaveFullWorkTripEvent]
  E --> F[WorkLoading] --> G[write Work/Trip/Labour/TripLabour to DB]
  G --> H[WorkActionSuccess snackbar] --> I["pop() -> dashboard reload"]
  D -->|invalid| J[inline errors / no-labour snackbar]
```

## 3. Add "Next Trip" (copy last)
```mermaid
flowchart LR
  A[Tap + on Current-Trip] --> B[NavigateToConfirmNextTripEvent]
  B --> C[BLoC finds last trip & labours]
  C --> D[NavigateToConfirmNextTripState]
  D --> E[dashboard listener pushes /confirm-next-trip]
  E --> F[ConfirmNextTripScreen prefilled]
  F --> G[Save as Next Trip]
  G --> H[SaveNextTripEvent: DB write] --> I[pop + reload dashboard]
```

## 4. Attendance on a trip
```mermaid
flowchart LR
  A[Trip row] --> B["push('/trip-details', extra trip)"]
  B --> C[LoadTripDetailsEvent] --> D[TripDetailsLoaded]
  D --> E[Toggle Present/Absent]
  E --> F[UpdateTripLabourEvent: DB write] --> D
  D --> G[Add/Edit/Remove labour]
  G --> H[dialogs + SaveTripLabour/DeleteTripLabour] --> D
```

## 5. Search (current)
User types in dashboard search → each keystroke `SearchDashboardEvent` → sets query → reload dashboard → `LoadDashboardDataEvent` filters trips by driver/tractor/tripNo/workType/place. No results → list shows nothing ("No trips in current session" message area). Clear (X) resets.

## 6. Delete a trip
```mermaid
flowchart LR
  A[Swipe left / minus / history delete] --> B[Confirm dialog]
  B -->|Cancel| Z[no change]
  B -->|Delete| C[DeleteSpecificTrip/RemoveLatest]
  C --> D[DB: delete Trip + cascade TripLabour prefix keys]
  D --> E[reload dashboard] 
  E -->|history| F[reload history after 300 ms]
```

## 7. Backup & restore
```mermaid
flowchart LR
  A[Settings Backup] --> B[SAF save .labourbackup]
  B --> C[serialize 4 boxes to JSON] --> D[write file] --> E[success snackbar]
  F[Settings Restore] --> G[SAF pick .labourbackup]
  G --> H[parse+validate 25MB/structure off-thread]
  H -->|fail| I[typed error dialog]
  H -->|ok| J[summary + overwrite confirm]
  J --> K[snapshot -> clear -> apply -> verify counts]
  K -->|fail| L[rollback snapshot + error]
  K -->|ok| M[success snackbar]
```

## 8. Draft recovery
User opens Add Trip with an existing draft → snackbar "You have an unsaved draft." → tap Restore → fields & labour list repopulated.

## 9 (PROPOSED) Native: authentication & role resolution
```mermaid
flowchart LR
  A[Launch] --> B[Auth State listener]
  B -->|signed out| C[Login]
  C --> D[Firebase Auth]
  D --> E[Fetch role (custom claims / role doc / CF)]
  E --> F[Route by role: Owner/Driver/Labourer home]
  B -->|signed in| E
```
Authz enforced server-side; client role only drives UI.

## 10 (PROPOSED) Native: owner assigns a job to driver
Create job → CF validates owner → Firestore job doc (ownerId) → notify driver (FCM) → driver updates status via server-validated function.

## 11 (PROPOSED) Native: offline write + reconcile
Driver updates offline → local queue → on reconnect replay via authorized write → conflict policy resolves (no silent overwrite) → audit log.

## Flow coverage gaps (current)
No onboarding, no login/logout, no password recovery, no role selection, no notifications, no session expiry, no permission-denial flow, no network-failure (offline app). These do not exist → documented as MISSING/not-applicable in current product; see `08-NATIVE-ANDROID/`.
