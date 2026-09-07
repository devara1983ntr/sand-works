# Third-Party Dependencies (current)

Status: VERIFIED from `pubspec.yaml` + `pubspec.lock`. Commit `2dd2fe4`.

## Runtime dependencies
| Package | Resolved | Purpose |
|---|---|---|
| flutter_bloc | 9.1.1 | state management |
| go_router | 17.2.3 | navigation |
| hive | 2.2.3 | NoSQL local store |
| hive_flutter | 1.1.0 | Flutter bindings |
| get_it | 9.2.1 | DI container |
| equatable | 2.0.8 | value equality for state/entities |
| dartz | 0.10.1 | `Either` functional errors |
| uuid | 4.5.3 | id generation |
| intl | 0.20.2 | date formatting |
| flutter_screenutil | 5.9.3 | responsive sizing |
| file_picker | 8.3.7 | SAF save/open (backup) |
| google_fonts | 8.1.0 | fonts (runtime fetch) |
| flutter_animate | 4.5.2 | UI animations |
| glassmorphism_ui | 0.3.0 | glass cards |
| path_provider | 2.1.5 | app-support dir for Hive |
| cupertino_icons | 1.0.9 | iOS-style icons |

## Dev dependencies
| Package | Resolved | Purpose |
|---|---|---|
| flutter_test | (SDK) | tests |
| flutter_lints | 6.0.0 | lint set |
| build_runner | 2.4.13 | codegen (hive adapters) |
| hive_generator | 2.0.1 | adapter codegen |
| mocktail | 1.0.5 | mocking in tests |

## Security/supply-chain observations (VERIFIED)
- **No Firebase / no networking packages.** Confirms offline architecture.
- Generated code committed: `*.model.g.dart` adapters present (typeIds 0–4) — keep in sync with `hive_generator` (`pub run build_runner`).
- `google_fonts` performs **network font fetch at runtime** → offline-first concern (fonts may not render as intended offline). Recommend bundling.
- Dependencies are recent/locked via `pubspec.lock`. No pinned CVE assessment performed in this sandbox (`UNVERIFIED` CVE status).

## Native (PROPOSED)
Replace with Android equivalents: Hilt (DI), kotlinx.coroutines/Flow, Room or Firestore, Coil (images), DataStore (prefs), Navigation Compose, WorkManager; no runtime font fetch (bundle fonts).
