# Dependency Policy

Status: PROPOSED (Phase 0). Rules for choosing and maintaining native dependencies. Purpose-first: every dependency must answer «What problem does this solve?».

## 1. Rules
- Prefer official AndroidX / Google / JetBrains libraries.
- Prefer **stable** versions; do not adopt alpha/beta merely because version number is larger (e.g., Material 3 stable is **1.4.0**; 1.5.0 is alpha — do not use).
- Verify current stable versions from official release notes **before implementation**; record them in the implementation spec (see Version Strategy below).
- Avoid unnecessary/abandoned libraries; avoid adding for popularity.
- Review transitive dependencies & license/security.
- Document each dependency's purpose.
- Run security scans (dependency audit) in CI.
- Periodically update within stable channels and re-run tests.

## 2. Proposed dependency set (each with purpose)
| Category | Library | Purpose | Justification |
|---|---|---|---|
| DI | Hilt (Dagger) | dependency injection | compile-safe, Jetpack integration |
| Async | Kotlin coroutines + Flow | concurrency, reactive state | core AndroidX |
| UI | Jetpack Compose + Material 3 (stable 1.4.0) + Navigation Compose | UI/design/nav | required |
| Lifecycle | lifecycle-viewmodel-compose, collectAsStateWithLifecycle | state collection | AndroidX |
| State | kotlinx StateFlow/SharedFlow | UDF state | core |
| Data | Cloud Firestore (Firebase BoM), Firebase Auth | cloud data/identity | required |
| Local | Room (only where local relational justified: outbox/cache), DataStore | local | see ADR-011 |
| Images | Coil | async image load/cache | only if photos added |
| Background | WorkManager | offline queue/backup | reliability |
| Parsing/JSON | kotlinx.serialization | DTO mapping | optional (only if needed) |
| Testing | JUnit, coroutines-test, Compose UI test, MockK/fakes, Robolectric, Firebase Emulator SDKs, rules-unit-testing | tests | required |
| DI testing | dagger hilt-android-testing | test DI | required |
| Ops | Crashlytics, (optional) Analytics, App Check | reliability | required |

## 3. Libraries explicitly NOT added (unless justified later)
Retrofit/OkHttp (no bespoke HTTP API — Firestore SDK), Mapbox/Google Maps (no GPS in v1), image-loading unless media added, DI alternatives, multiplatform frameworks.

## 4. Anti-goal
Do not add libraries to look sophisticated. Add only when it solves a concrete, justified problem.

## 5. Version Strategy
Record verified stable versions in this section at implementation time (before code). Current environment date: 2026-09. Anchor (verify from official notes at implementation):
- Material 3 (androidx.compose.material3): stable **1.4.0** (VERIFIED via androidx release notes). 1.5.0-alpha27 is NOT stable → do not adopt.
- Compose BOM: recent stable 2026.x (e.g., 2026.08.00) maps Compose core ~1.12.0 stable; **confirm BOM maps to stable core before use**.
- Verify before implementation (mark each): Android Studio, AGP, Gradle, Kotlin, Compose BOM, Compose core, Material3, Navigation Compose, Lifecycle, Hilt, Firebase BoM, Room, WorkManager, Coil, Crashlytics, App Check.
- Compose 1.12+ may require compileSdk 37 + AGP 9 (per release notes) → plan toolchain accordingly; do not force an AGP/compileSdk bump unless required by stable deps.

## 6. Verification
Version values above are from public androidx release notes as of audit date; **re-verify at implementation** before pinning. No dependencies resolved/build yet.
