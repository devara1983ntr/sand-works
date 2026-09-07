# Flutter → Native Android Migration Matrix

Purpose: comprehensive mapping of every current Flutter implementation element to a native Android equivalent. Status legend per `00-AUDIT-INDEX.md`. Commit audited `2dd2fe4`.

## Tech/dependency mapping
| Flutter element | Current version | Native Android equivalent | Notes |
|---|---|---|---|
| Dart / sealed classes | SDK ^3.11.0 | Kotlin sealed classes / data classes | |
| Flutter widgets/Material3 | dark theme | Jetpack Compose + Material 3 | rebuild UI |
| flutter_bloc | 9.1.1 | ViewModel + StateFlow (UDF/MVI) | state layer changes |
| go_router | 17.2.3 | Navigation Compose / Type-safe nav | replace `extra`-based passing w/ SavedStateHandle |
| get_it | 9.2.1 | Hilt (Dagger) | |
| dartz Either | 0.10.1 | Kotlin Result / sealed error | map Failure types |
| equatable | 2.0.8 | data classes | |
| Hive | 2.2.3 | Room (relational) or DataStore | offline store |
| hive_generator / .g.dart | 2.0.1 | Room annotation processor | |
| uuid | 4.5.3 | UUID / server ids | |
| intl | 0.20.2 | java.time + DateTimeFormatter / Android strings | localization |
| flutter_screenutil | 5.9.3 | Compose adaptive layouts | drop fixed 360x690 |
| file_picker | 8.3.7 | SAF (ACTION_CREATE_DOCUMENT / OPEN_DOCUMENT) + Photo Picker | backup |
| google_fonts | 8.1.0 | bundled font resources | avoid runtime fetch |
| flutter_animate | 4.5.2 | Compose animation APIs + Material motion | reduced-motion aware |
| glassmorphism_ui | 0.3.0 | M3 tonal surfaces (re-evaluate glass) | perf/design |
| path_provider | 2.1.5 | context.filesDir / Room | |
| cupertino_icons | 1.0.9 | Material icons | |
| (none) | — | Coil (images) | new |
| (none) | — | WorkManager (background) | new |
| (none) | — | Firebase SDKs | new (see Firebase docs) |

## Build/platform mapping
| Flutter | Native |
|---|---|
| `pubspec.yaml` version 1.0.0+1 | versionCode/versionName via Gradle |
| `android/app/build.gradle.kts` (AGP 8.11.1, Kotlin 2.2.20, Gradle 8.14) | Gradle Kotlin DSL (keep/upgrade) |
| minSdk 24 | keep/raise (Firebase min) |
| compileSdk/targetSdk = Flutter defaults | pin explicitly |
| release keystore via key.properties | Play App Signing + CI secret store |
| proguard-rules.pro | R8/proguard with Compose keep rules |
| AndroidManifest (no permissions) | add only required; runtime requests contextual |
| `.metadata` Flutter revision | n/a |

## Source-file mapping (areas)
| Flutter area | Purpose | Native area |
|---|---|---|
| `lib/core/database/hive_setup.dart` | box open/adapters | `:data:local` Room db + migrations |
| `lib/core/error/failures.dart` | typed failures | sealed `Failure` sealed class |
| `lib/core/usecases/` | usecase base | domain `UseCase` / usecase classes |
| `lib/features/work/domain/**` | entities/usecases/repo iface | `:domain` |
| `lib/features/work/data/**` | repo impl + data source + models | `:data:local` / `:data:remote` + mappers |
| `lib/features/work/presentation/bloc/**` | events/states/bloc | ViewModel + UiState + sealed UiEvent |
| `lib/features/*/presentation/**` screens | screens | Compose screens per role |
| `lib/shared/**` layout+widgets | shared components | `:core:designsystem` composables |
| `lib/routes/app_router.dart` | routes | Navigation graph |
| `lib/config/di` | DI | Hilt modules |
| `lib/theme` | theme | Compose theme |
| `lib/main.dart` | boot | `Application` + MainActivity + DI init |

## Screen parity (see SCREENS.md S-IDs)
Splash→Compose splash; Dashboard→Owner Home; History→History; Analytics→Reports; Settings→Settings+Account; Trip Details→Job/Trip detail; Add/Edit Work→Job/Trip editor; Confirm Next Trip→Copy-next-trip confirm. `/details` orphan → remove or make reachable day detail.

## Migration risk summary by area
| Area | Complexity | Risk | Notes |
|---|---|---|---|
| Data/domain rules | Low | Low-Med | well-scoped; re-implement + test trip numbering |
| UI rewrite | High | Med | UX redesign opportunity; reuse specs |
| Offline store | Med | Med | Hive→Room; schema version/migrate |
| Auth/RBAC/Firebase | High | High | net-new; secure-by-design |
| Notifications/sync | High | Med-High | new; offline-first tension |
| Backup/DR | Med | Med | make encrypted/signed; WorkManager |

## Full feature parity table
See `FEATURE-PARITY-MATRIX.md` (every feature F-01..F-29).
