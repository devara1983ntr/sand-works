# API Specification

Status: VERIFIED. Commit `2dd2fe4`.

## 1. Remote API
None exists (see `API.md`). Everything below documents the **domain repository contract**, which behaves as the local application "API". A future Firebase API is specified only in `08-NATIVE-ANDROID/FIREBASE-ARCHITECTURE.md` and `FIREBASE-DATABASE.md` as PROPOSED.

## 2. Repository interface `WorkRepository` (current "API")
Signatures (dartz `Either<Failure,T>`; local, Hive-backed).

### Work
- `getWorks() → Either<Failure,List<Work>>` — all works sorted by `createdAt` desc.
- `getWorkByDateAndSession(String date, String session) → Either<Failure,Work>` — null → `DatabaseFailure('Work not found')`.
- `saveWork(Work) → Either<Failure,void>`.

### Trip
- `getTripsForWork(String workId) → Either<Failure,List<Trip>>` — sorted by tripNumber asc.
- `getAllTrips() → Either<Failure,List<Trip>>` — sorted by createdAt desc.
- `saveTrip(Trip)`, `deleteTrip(String tripId)` (cascades attendance).

### Labour
- `getLabours() → Either<Failure,List<Labour>>` — sorted by name asc.
- `saveLabour(Labour)`.

### TripLabour
- `getLaboursForTrip(String tripId)`, `getLaboursForTrips(List<String>)`.
- `saveTripLabour`, `saveTripLabours(List)` (replace-all for a trip).
- `deleteTripLabour(String id)`.

### Business usecase
- `CalculateNextTripNumberUseCase.call(date)`.

All repository methods return `Left(DatabaseFailure(...))` on thrown errors; there is no validation error surfaced at repository layer (validation lives in screens/usecases).

## 3. Failure taxonomy (`core/error/failures.dart`)
- `Failure(message)` abstract; `DatabaseFailure`, `ValidationFailure` (ValidationFailure declared but **not used** anywhere — dead type; validation is inline).
- UI shows `message` string directly for errors.

## 4. Event/state "contracts" (Bloc) — see STATE-MANAGEMENT.md
WorkEvents: LoadDashboardData, AddQuickTrip (unused), NavigateToConfirmNextTrip, SaveNextTrip, RemoveLatestTrip, DeleteSpecificTrip, LoadTripDetails, SaveFullWorkTrip, SaveTripLabour, SaveLabour, UpdateTripLabour, DeleteTripLabour, SearchDashboard, FilterDashboard.
HistoryEvents: LoadHistory.

## 5. Findings
- No versioned wire contract to preserve.
- `ValidationFailure` is unused (dead).
- Error messages are user-facing strings concatenated with raw exception text (`...$e`) — minor info-leak/quality concern.

## 6. Native
Design explicit repository contracts + DTO mapping and a real Firebase DataSource later (PROPOSED). Keep domain independent of Firebase.
