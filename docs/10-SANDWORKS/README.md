# SAND WORKS — Authoritative Product Direction (docs/10-SANDWORKS)

Status: **LOCKED PRODUCT DIRECTION** — this package is the controlling specification for the native SAND WORKS application. It supersedes the earlier `08-NATIVE-ANDROID` + `09-IMPLEMENTATION` material wherever they conflict with this directive (per directive §47). Planning only — no code, no Firebase resources, no fake data.

## Project frame (locked)
| Field | Value |
|---|---|
| App / brand | **SAND WORKS** |
| Owner | Ramesh Sahu |
| Project type | Private personal/family application |
| Distribution | Private APK / personal devices only |
| Public release | NO |
| Google Play | NO |
| Legacy Flutter data migration | NO — START FRESH |
| Package / applicationId | **com.roshan.sandworks** |
| Roles | OWNER · DRIVER · LABOURER (no admin; one owner) |

## Brand assets (immutable source of truth)
| Asset (repo root on GitHub `devara1983ntr/sand-works`) | Purpose |
|---|---|
| `sand_works_app_icon_master.png` (1254×1254 RGBA) | Android application icon (launcher, compact brand, auth header, notification identity) |
| `sand_works_logo_master.png` (1024×1024 RGBA) | Full SAND WORKS logo (splash, About, brand surfaces, docs) |
| `SAND_WORKS_brand_assets_locked.zip` | Supplied locked brand set |

**DO NOT**: regenerate/redesign/recreate the artwork, create SVG, trace PNG→SVG, alter colours/typography, replace excavator/truck/sand elements, add/remove elements, invent another icon, or use AI substitutes. **Allowed**: resize, crop only when technically required, package into Android resource densities, create density PNG copies, launcher references, lossless PNG optimisation. Never modify the masters.

## Source-of-truth hierarchy (this package supersedes where conflicting)
1. Explicit user/owner decisions (this directive)
2. `docs/10-SANDWORKS/*` (this package)
3. `docs/03-ENGINEERING/AGENT.md` §14 rules (binding; immutable)
4. `docs/08-NATIVE-ANDROID/` + `docs/09-IMPLEMENTATION/` — retained as reference/architecture continuity, RE-RECONCILED to this scope
5. Flutter reference app (`lib/`) — behavioural reference only
6. Agent inference — never

## Contents of this package
| File | Purpose |
|---|---|
| `README.md` (this) | Frame, assets, authority, readiness |
| `DIRECTIVE-REGISTER.md` | Decision resolution (D-1..D-8 + new SAND WORKS decisions), incl. honest Firebase Blaze/Spark handling |
| `PRODUCT-FREEZE.md` | V1 scope: MUST / SHOULD / OUT OF SCOPE / DEFERRED |
| `ROLE-AND-USER-MODEL.md` | OWNER/DRIVER/LABOURER, approval, temporary assignment |
| `FEATURE-CATALOG.md` | Feature-by-feature, role-mapped, statused |
| `SCREEN-CATALOG.md` | Owner/Driver/Labourer screens + new screens |
| `DATA-MODEL.md` | Core entities/collections, immutability, relationships |
| `MONEY-ENGINE-SPEC.md` | Rate snapshotting, distribution, daily closure idempotency, integer currency |
| `SCHEDULING-SPEC.md` | 19:30 daily summary, Spark-vs-Blaze honesty |
| `SECURITY-RBAC.md` | Backend-authoritative RBAC, scoping, audit |
| `NOTIFICATION-ALERT-SPEC.md` | Notifications A–F + owner alert (honest Android limits) |
| `EXPORT-REPORT-SPEC.md` | PDF/CSV owner-only export |
| `OFFLINE-SYNC-SPEC.md` | Offline behaviour, idempotency, conflict |
| `UX-DESIGN-SYSTEM.md` | Industrial design language, colour, type, M3, shape, nav |
| `TEST-AND-QUALITY-SPEC.md` | Test matrix + quality bar |
| `ASSET-INVENTORY.md` | Locked brand asset register (extracted zip) + integrity rules |
| `BLOCKERS.md` | **Separate blocker register** (go-live gate + asset confirmations) |
| `NAVIGATION.md` | Role-scoped navigation + Back + deep links |
| `SCREEN-STATE-CONTRACT.md` | State/error/loading/empty/offline honesty contract |
| `BACKEND-OPERATIONS.md` | Cloud Function ops B-01..18 + rule-compliant queries |
| `WORKFLOW-STATE-MACHINES.md` | User/trip/closure/assignment/alert state machines |
| `CONCURRENCY-IDEMPOTENCY-AUDIT.md` | Idempotency, concurrency, audit |
| `FORMS-VALIDATION.md` | Form field + cross-field + server validation |
| `IMPLEMENTATION-CONTROL.md` | Planning control mapping to `09-IMPLEMENTATION` + task inventory |

## Readiness
**Planning READY per scope; execution gated by environment.** Decisions this directive resolves are resolved. Remaining execution blockers are **environmental/credentials** (real Firebase project; Blaze plan if Storage/Functions/Server-authoritative scheduling required — directive explicitly forbids faking these on Spark) and **signing identity** (new dedicated keystore, never the legacy one). Full detail in `DIRECTIVE-REGISTER.md` + `IMPLEMENTATION-CONTROL.md`.
