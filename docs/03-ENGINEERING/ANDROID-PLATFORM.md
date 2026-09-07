# Android Platform (current Flutter host)

Status: VERIFIED (static). Commit `2dd2fe4`.

## Host configuration
| Item | Value | Source |
|---|---|---|
| Package/namespace/applicationId | `com.roshan.labourparty` | `build.gradle.kts` |
| Flutter/Dart SDK floor | `sdk: ^3.11.0` | `pubspec.yaml` |
| Flutter revision (`.metadata`) | `90673a4eef...` (channel stable) | `.metadata` |
| minSdk | 24 | `build.gradle.kts` |
| compileSdk | `flutter.compileSdkVersion` (Flutter default) | `build.gradle.kts` |
| targetSdk | `flutter.targetSdkVersion` | `build.gradle.kts` |
| Android Gradle Plugin | `8.11.1` | `settings.gradle.kts` |
| Kotlin (Gradle) | `2.2.20` | `settings.gradle.kts` |
| Gradle | `8.14` | `gradle-wrapper.properties` |
| Java toolchain | 17 | `build.gradle.kts` |
| versionName / versionCode | `1.0.0` / `1` (from pubspec `1.0.0+1`) | pubspec |
| Release | minify+shrink true, proguard file present; signing from `key.properties` (not committed) | `build.gradle.kts` |

## Platform scaffolding present
- Only `android/` is fully scaffolded; no `ios/`, `web/`, `linux/`, `windows/`, `macos/` directories committed (`.metadata` lists them but directories absent).
- `MainActivity.kt` = default `FlutterActivity`.
- Launcher icons & launch/normal themes under `res/`.

## Manifest (VERIFIED)
- **No `<uses-permission>` entries** — including no `INTERNET`. Confirms offline-only & no runtime permissions.
- `<queries>` for `PROCESS_TEXT` (Flutter default). Activity `android:exported="true"` with MAIN/LAUNCHER filter; `configChanges` covers orientation/keyboard/screenSize/locale etc.; `windowSoftInputMode=adjustResize`.
- No deep-link intent filters; no exported components beyond launcher.

## Release signing (VERIFIED)
- Signing uses `android/key.properties` (git-ignored; only `key.properties.example` committed). Release task throws if key.properties missing.
- **CRITICAL finding:** `android/app/keystore.jks.bak` (2,690-byte DER file; begins `30 82 ...` = ASN.1; contains a PKCS#8 v0 `02 01 00` private-key marker; named "keystore…bak") is **tracked in the repository** (added in commit `55b2144`). Its name implies a signing/upload keystore backup. `.gitignore` ignores `*.jks`/`*.keystore` but **not** `*.jks.bak`, so it slipped through. Treat as potential private-key exposure → see `04-SECURITY/SECRETS-AUDIT.md`. (Only structural facts reported; contents not reproduced.)

## Findings
| ID | Finding | Severity |
|---|---|---|
| AND-1 | Keystore-like private-key file committed (`.jks.bak`) | CRITICAL |
| AND-2 | compileSdk/targetSdk not pinned (Flutter defaults) | LOW |
| AND-3 | No versionCode build automation/config flavours | LOW |
| AND-4 | No CI/CD pipeline config in repo | MEDIUM (see 07-OPERATIONS) |

## Native (PROPOSED)
Pin compileSdk/targetSdk/minSdk deliberately; define build variants & flavours if needed; use Play App Signing; store signing key outside repo; enable App Check when Firebase added. See `08-NATIVE-ANDROID/`.
