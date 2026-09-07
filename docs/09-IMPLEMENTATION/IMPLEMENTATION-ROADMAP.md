# SAND WORKS — Implementation Roadmap

Status: **RECONCILED to `docs/10-SANDWORKS/` (locked scope).** Supersedes the retired S-V1 single-owner roadmap. Planning only.

## North star (final directives, locked)
Roles **OWNER/DRIVER/LABOURER (no admin)** · package **`com.roshan.sandworks`** · brand **SAND WORKS** · **START FRESH** (no legacy migration) · private distribution (no Google Play public release) · money = accrued-totals only (never "payment") · default rate ₹200 · equal-split default distribution · daily summary 19:30 IST configurable · WhatsApp share (driver) · owner-only export/report · approval-before-access · temp-assignment expiry enforced · tractor registry (init Sonalika/John Deere).

## Delivery model
- **Not public release.** Delivery target = private production APK to owner; build requires owner-supplied Firebase project + signing + (Blaze decision) — see `PHASE-GATES.md` and `10-SANDWORKS/BLOCKERS.md`.
- Nine implementation phases P1..P9 map onto **Gates Gate-0..Gate-9** (Gate-0 = frozen spec, executed once; Gate-n = phase-n exit).

## Phase → Gate → Exit criteria
| Phase | IDs | Exit gate | Exit criterion (summary) |
|---|---|---|---|
| P1 Foundations & identity | SW-101..108 | Gate-1 | Project `com.roshan.sandworks`, DI/nav/design-system/theme/error handlers in place; spec traceable |
| P2 Data & offline | SW-201..204 | Gate-2 | Room/outbox/repos; deterministic replay; idempotency + conflict primitives proven |
| P3 Auth, security, RBAC, rules, CF, audit | SW-301..309 | Gate-3 | Real Firebase wired; approvals, RBAC, Firestore/Storage rules + CF ops B-01..B-18 + audit working under rules; attack suite green |
| P4 Money engine & scheduling | SW-401..404 | Gate-4 | Rate snapshot, distribution, daily closure (idempotent), leaderboards correct; Blaze/fallback decided |
| P5 OWNER surfaces | SW-501..509 | Gate-5 | All owner screens fulfil sub-checklist (states/a11y/responsive/tests) |
| P6 DRIVER surfaces | SW-601..605 | Gate-6 | All driver screens fulfil sub-checklist |
| P7 LABOURER surfaces | SW-701..704 | Gate-7 | All labourer read-only screens fulfil sub-checklist |
| P8 Notifications & alerts | SW-801..803 | Gate-8 | FCM + centre A–F + owner alert comply with NOTIFICATION-ALERT-SPEC |
| P9 Quality & release | SW-901..906 | Gate-9 | Test matrix, security retest, assets, release readiness, handover |

## Spec-derived architecture spine (final, per `docs/10-SANDWORKS` + non-conflicting `docs/08-NATIVE-ANDROID`)
Kotlin + Jetpack Compose, offline-first → Firebase (Auth/Firestore/Storage/Functions/FCM/App Check), server-authoritative privileged ops via Cloud Functions, deterministic idempotent outbox, revision-based concurrency, Material 3 + locked SAND WORKS brand, per-role Compose surfaces with own/org scoping.

## Reference assets (immutable)
PNG logo/icon are **locked and immutable** (no SVG/regeneration/redraw). The canonical master set is under owner confirmation (SW-BLK-A1/A2). See `10-SANDWORKS/ASSET-INVENTORY.md`.
