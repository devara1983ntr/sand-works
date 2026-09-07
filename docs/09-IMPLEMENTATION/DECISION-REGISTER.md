# SAND WORKS — Decision & Blocker Register (implementation layer)

Status: **RECONCILED to `docs/10-SANDWORKS/DIRECTIVE-REGISTER.md`, `PRODUCT-FREEZE.md`, `BLOCKERS.md`.** Retired S-V1 single-owner decisions (D/BLK/IMPL-xxx rows) are **superseded** and removed.

## Governance rule
A business/design/asset blocker is cleared **only by the owner** (AGENT §14.23). An agent may not clear it itself and may not disguise a blocker as a task. When a blocker is raised it is recorded here and set on the affected task (`Status=BLOCKED`). Specification (SW-BLK-1..6 class depends) vs go-live are kept in the one register but classified.

## Resolved decisions (locked; do not re-ask — source `DIRECTIVE-REGISTER.md`)
Roles OWNER/DRIVER/LABOURER (no ADMIN) · one owner (Ramesh Sahu) · driver + labourer are authenticated users requiring approval · package `com.roshan.sandworks` · brand **SAND WORKS** · **START FRESH** (no legacy migration) · no public Play release · driver creates trips · reports PDF+CSV owner-only · default rate ₹200 · default equal-split distribution · daily summary 19:30 IST configurable · accrued-totals-only wording (never "payment") · WhatsApp share (driver) · temp-assignment expiry enforced · tractor registry (init Sonalika/John Deere) · new signing keystore · no admin/delegate · labourer read-only.

## Execution blockers (govern go-live) — mirror of `10-SANDWORKS/BLOCKERS.md`
> **TWO-TIER GATING (R-1.1):** Development is **emulator-first** — the local Firebase Emulator Suite (Auth/Firestore/Storage/Functions) + debug builds let an independent agent **author and test every SW task now**, so **no blocker is required before coding/authoring any area**. The blockers below are required for **real-cloud integration and/or release/go-live (verified DONE)**, NOT to begin coding. Tier column states where each applies.
| ID | Class | What is missing | Blocks | Tier | Smallest input needed |
|---|---|---|---|---|---|
| SW-BLK-1 | CREDENTIAL/ENVIRONMENT | Real Firebase project + `google-services.json` + config | real auth/Firestore/rules/CF/Storage/App-Key verify & go-live | **real-cloud integration + go-live** | Owner creates project; env config; never commit keys |
| SW-BLK-2 | BUSINESS DECISION / EXTERNAL | Blaze vs Spark plan | SWF-24/25/26, cloud export/closure, CF cloud deploy, Storage-in-cloud | **real-cloud integration + go-live** | Owner decides; if Spark → documented fallbacks (never fake) |
| SW-BLK-3 | SECURITY/ENV | Release signing keystore (new) | Release build & private APK | **release/go-live only** (debug needs none) | Owner generates keystore; env/ignored; offline backup |
| SW-BLK-4 | CREDENTIAL/ENVIRONMENT | FCM sender/project creds | SWF-15/16 real push delivery & go-live | **real-cloud integration + go-live** | Provided with Firebase project |
| SW-BLK-5 | DESIGN/UX (approval) | Notification + alert copy wording (approved copy only) | Final alert/notification strings | **finalisation/release only** | Owner/approved copy |
| SW-BLK-6 | DESIGN SPECIFICATION | Wireframe/UX mockups approval | O/D/L screens final polish (not initial build) | **finalisation/release only** | Owner approves ASCII wireframes + layout |

No SW-BLK blocks initial authoring of any SW task. Coding proceeds to the emulator/local boundary now; real-cloud & release DONE waits on the Tier shown.

## Blocking asset confirmation (must not guess) — Tier: asset finalisation / release only (never blocks coding)
| ID | Class | What is missing | Smallest input |
|---|---|---|---|
| SW-BLK-A1 | MISSING ASSET (confirm) | Canonical masters: zip `master/` PNGs **1536×1536**; repo-root `sand_works_*_master.png` **1254×1254 & 1024×1024** | Owner confirms canonical set (recommended: extracted `sand_works_brand_assets/master/`, cross-checked) |
| SW-BLK-A2 | MISSING ASSET (confirm) | Zip README calls logo "horizontal brand logo" but `sand_works_logo_master.png` is **1536×1536 square** | Owner confirms logo crop/aspect usage |

## Non-blocking confirmations (do not gate build)
Data retention/history window · labourer self-registration vs owner-created · cloud backup on Blaze · dark/light default theme.
