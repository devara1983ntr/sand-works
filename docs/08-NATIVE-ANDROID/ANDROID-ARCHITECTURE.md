# Native Android Architecture

Status: PROPOSED (Phase 0). Supersedes the earlier `ANDROID-ARCHITECTURE.md` outline with the reconciled spec. Dependency direction: **Presentation → Domain ← Data**. Domain must not depend on Firebase/Android.

## 1. Stack (baseline) — verify versions before implementation (see VERSION STRATEGY & DEPENDENCY-POLICY)
Kotlin + Jetpack Compose + Material 3 (stable, not alpha) + Navigation Compose + ViewModel/Lifecycle + StateFlow/Flow (UDF) + Clean Architecture + Hilt + Coroutines + Repository + Room (only where justified) + DataStore + Coil + WorkManager + Firebase (Auth, Firestore, Storage?, Cloud Functions, FCM, App Check, Crashlytics; optional Analytics/Remote Config).

## 2. Layer responsibilities
```
        Compose UI  (presentation; collects UiState, emits UiEvent/Intents)
            │ UiState / events
        ViewModel (maps events→use cases→state; holds per-screen state)
            │
        Use Cases (domain business rules)
            │
        Domain (entities + repository interfaces)   <- no Firebase/Android imports
            │
        Repositories (implement domain interfaces)
            │
        Data (Firebase datasources, Room, DataStore, WorkManager jobs) + mappers
```
Rule: UI never calls Firebase/Room/business logic directly. UseCase orchestration in ViewModel. Repository is the seam for local/remote.

## 3. State management (UDF / MVI)
```
User Action → ViewModel (intent) → UseCase → Repository → DataSource → Result
      → StateFlow<UiState> (sealed) → Compose collectAsStateWithLifecycle
```
Explicit states per screen/model:
`Initial · Loading · Success · Empty · Error · Offline · Unauthorized · Forbidden · Refreshing · Submitting · SuccessWithStaleData`.
- No arbitrary global mutable state; scoped ViewModels (SavedStateHandle) survive config/process recreation.
- One-way navigation handled explicitly (see ADR-013); avoid navigation-as-state where avoidable.

## 4. Module structure (avoid over-engineering)
Recommended single `:app` with clear package layering for an MVP **or** light modularisation:
```
app/
core/  (common, designsystem, navigation, model, security, firebase, database, testing)
feature/ (auth, dashboard, work, attendance, notifications, profile, settings, reports, users/drivers/labourers as needed)
domain/ (model, repository, usecase)
data/  (firebase, local, repository)
```
Decision: For a product of this size, **prefer a single app module with package boundaries**; split into modules only when compile/test/team size justify it. Do not force the example tree.

## 5. Concurrency & lifecycle
- Coroutines; `Dispatchers.IO` for DB/network; `Dispatchers.Default` for CPU; main-safe ViewModels; Flow collection in UI lifecycle-aware.
- WorkManager for reliable background (offline queue replay, backup, notification refresh).
- Repository operations suspend; datasources are the only place touching Firebase/Room.

## 6. Error & failure mapping
- Repository returns domain results mapping failures (network, auth, permission/Forbidden, offline, validation) — not raw Firebase exceptions leaking to UI.
- UI state carries typed error so the layer can offer retry/recovery.

## 7. Roles & authorization seam
- `AuthRepository` exposes `StateFlow<AuthUser?>` (uid, role). ViewModel uses role only to choose which **screens/actions** to show.
- Every sensitive op still goes to a server-enforced boundary (rules/CF). Never a client-only authorization decision.

## 8. Testing hooks
- Repository interfaces → fake/mock repositories in tests only.
- DI (Hilt) allows swapping Firebase datasources for emulator/local in tests.
- ViewModels unit-tested via use-case fakes; UiState pure.

## 9. Anti-goals
No business/backend logic in composables; no client-only authz; no global mutable state; no main-thread I/O; no network on main; no over-modularization.
