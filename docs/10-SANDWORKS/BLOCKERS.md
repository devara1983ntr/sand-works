# BLOCKERS — SAND WORKS (Separate register)

> This register is intentionally SEPARATE so a coding agent can immediately see what blocks go-live versus what is specified. Nothing here is disguised as a task. A coding agent MUST NOT begin building the affected areas until the corresponding blocker is resolved by the owner/environment.

Blocker classes (per AGENT §14.23): BUSINESS DECISION · DESIGN SPECIFICATION · MISSING ASSET · BACKEND CONTRACT · CREDENTIAL/ENVIRONMENT · EXTERNAL DEPENDENCY · SECURITY REQUIREMENT.

## Hard execution blockers (block go-live)
| ID | Class | What is missing | Why required | What it blocks | Smallest input needed |
|---|---|---|---|---|---|
| SW-BLK-1 | CREDENTIAL/ENVIRONMENT | Real Firebase project + `google-services.json` + config | Auth/Firestore/Storage/Functions/FCM/App Check need a real project | All backend/security/CF phases & release | Owner creates Firebase project; provide config via env; NEVER commit keys |
| SW-BLK-2 | BUSINESS DECISION / EXTERNAL DEPENDENCY | **Blaze vs Spark** Firebase plan | Cloud Storage (photos/export) + Cloud Functions (scheduling/closures/backup) require Blaze | SWF-24 (Storage photos), SWF-25 (cloud-scheduled closure), SWF-26 (cloud backup), cloud CSV/PDF export | Owner decides plan. If Spark → implement documented fallbacks (never fake Storage/scheduling) |
| SW-BLK-3 | SECURITY REQUIREMENT / ENV | Dedicated **release signing keystore** | Private APK distribution needs a signing identity; SEC-1 hygiene forbids legacy keystore | Release build & private APK | Owner generates keystore; store in env/ignored file; secure offline backup |
| SW-BLK-4 | CREDENTIAL/ENVIRONMENT | FCM sender/project creds for notifications + alert | Owner alert & notifications A–F need FCM | SWF-15/16 | Provided with Firebase project |
| SW-BLK-5 | DESIGN/UX | Approval/decision on **notification + alert copy wording** (approved copy only) | No fabricated copy (AGENT §14.13) | Final alert/notification strings | Owner/approved copy for alerts, warning message templates, daily summary wording |
| SW-BLK-6 | DESIGN SPECIFICATION | **Wireframe/UX mockups** approval for role dashboards | Visual/UX lock before heavy UI build | Owner/Driver/Labourer screens final polish | Owner approves ASCII wireframes + layout direction (see WIREFRAMES.md) |

## Blocking asset confirmation
| ID | Class | What is missing | Why required | Smallest input |
|---|---|---|---|---|
| SW-BLK-A1 | MISSING ASSET (confirm) | Canonical masters: the zip `master/` PNGs are **1536×1536**; the repo-root `sand_works_*_master.png` are **1254×1254 and 1024×1024**. | Which set is the locked master for launcher/brand? Must not guess. | Owner confirms canonical master set (recommended: treat the extracted `sand_works_brand_assets/master/` as authoritative, cross-checked to the two root uploads) |
| SW-BLK-A2 | MISSING ASSET (confirm) | Zip README calls logo "horizontal brand logo" but `sand_works_logo_master.png` (zip) is **1536×1536 square**. | Aspect handling for splash/About depends on true logo aspect. | Owner confirms intended logo crop/aspect usage |

## Non-blocking confirmations (do not gate build)
- Data retention window (history/audit) for the private app.
- Labourer self-registration enabled vs owner-created accounts (directive implies self-registration requiring approval).
- Whether cloud backup is wanted once Blaze is confirmed.
- Dark/light default theme.

## Owner/operator input that IS resolved (do not re-ask)
Roles OWNER/DRIVER/LABOURER (no admin) · one owner (Ramesh Sahu) · package `com.roshan.sandworks` · brand SAND WORKS · START FRESH · no public release/Play · default rate ₹200 · default distribution equal split · summary 19:30 IST · accrued-totals-only wording · WhatsApp share (driver) · owner-only export · approval before access · temp-assignment expiry enforced · tractors registry (init Sonalika/John Deere) · new signing keystore.

## Guidance
Before a coding agent builds an area depending on a BLOCKED item, it must STOP and report (AGENT §14.23) rather than fabricate. All other areas are fully specified and can be built to READY once SW-BLK-1..3 clear.
