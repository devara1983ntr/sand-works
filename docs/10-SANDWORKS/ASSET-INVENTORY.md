# BRAND ASSET INVENTORY — SAND WORKS

Register of the locked brand assets (extracted from `SAND_WORKS_brand_assets_locked.zip`). Source-of-truth rule: the supplied PNG artwork is immutable. Do NOT redraw/regenerate/trace/SVG/recolor/replace. Allowed: resize, crop only when technically required, density copies, launcher references, lossless PNG optimisation.

Repository path (this repo root after extraction): `sand_works_brand_assets/`

## Master assets (immutable source)
| Asset | Repo path | Size (px) | Purpose |
|---|---|---|---|
| App icon master | `sand_works_brand_assets/master/sand_works_app_icon_master.png` | 1536×1536 | Android launcher/icon source |
| Logo master | `sand_works_brand_assets/master/sand_works_logo_master.png` | 1536×1536 | Full brand logo source |
> NOTE: A separate copy exists at repo root: `sand_works_*_master.png` (1254×1254 icon; 1024×1024 logo) as originally uploaded. Confirm canonical master set → BLOCKERS SW-BLK-A1/A2. Treat `sand_works_brand_assets/master/` as authoritative unless owner says otherwise.

## Logo sizes (resized copies for UI/splash/About/docs)
`sand_works_brand_assets/logo/sand_works_logo_{256,384,512,768,1024,1536}.png`

## Android launcher (density PNGs)
`sand_works_brand_assets/android/mipmap-{mdpi,hdpi,xhdpi,xxhdpi,xxxhdpi}/ic_launcher.png` (48/72/96/144/192)

## Play / icon working copies
`sand_works_brand_assets/android/play/sand_works_icon_512.png` (512) · `sand_works_icon_1024.png` (1024)

## Usage mapping
| Surface | Asset |
|---|---|
| Launcher | density ic_launcher (from app icon master) |
| Splash | full logo (large) |
| Auth header | app icon (compact) |
| About / brand surfaces | full logo |
| Notification identity | app icon |
| Docs | full logo / icon |

## Integrity rules (AGENT §14.10–14.12)
- No fabricated/regenerated/substitute logo or icon. No emoji/random-icon-as-brand. No AI substitutes.
- If a required derivative can't be produced from these masters, BLOCKED — MISSING ASSET (report), never invent.
- Never modify master files; store derivatives separately and regenerate deterministically from masters when needed.

## Package / app identity (locked)
Package/applicationId: `com.roshan.sandworks`. Display name: `SAND WORKS`. Never `com.roshan.labourparty`.
Signing: debug = standard; release = new dedicated keystore (never legacy), env/ignored creds, offline backups, never committed (AGENT §7).
