# EXPORT & REPORT SPEC — SAND WORKS

Owner-only export. Drivers/labourers never receive owner export.

## Export products
| Format | Purpose | Content |
|---|---|---|
| **PDF** (primary human-readable) | readable report | date range, total trips, tractor totals, driver totals, labour totals, money totals, working days, absent days, relevant summaries |
| **CSV** (secondary data) | spreadsheet analysis | detailed raw records |

## Breakdowns (multi-tractor reporting §36)
- ALL TRACTORS total
- EACH tractor total (e.g., Sonalika, John Deere)
- by driver
- by labourer
- by date
Do not merge records incorrectly; each total derives from real persisted trips with correct grouping.

## Rules
- Export only OWNER (RBAC + rules + CF).
- Produced from real data; never fabricated/sample.
- Human-readable PDF via a server/trusted renderer or a documented client render from rule-scoped data; CSV raw. If cloud Storage (Blaze) is used, files are private + signed URLs with expiry; owner-only. On Spark, local PDF/CSV generation (owner device) is acceptable but still owner-scoped and rule-guarded; do not fake a cloud export URL.
- Export operations audited (who, when, scope).
- No PII beyond the family data; no secrets.

## PDF sections (draft structure)
Header (SAND WORKS, date range, owner), Summary (total trips, money), Tractor breakdown, Driver breakdown, Labourer breakdown, Money totals, Working/absent days, footnotes (generated timestamp, real source data note).

## Tests
Export authorization (driver/labourer denied), correct breakdown arithmetic, real-data-only, empty-range handling (truthful empty, not fabricated), file validity (PDF/CSV open), audit event on export, signed-URL expiry (Blaze), no fake cloud export on Spark.
