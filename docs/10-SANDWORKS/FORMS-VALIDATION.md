# FORMS & VALIDATION — SAND WORKS

Every form: field type/required/validation/format/keyboard/focus/error/server-validation/duplicate/unsaved/submit/cancel/reset/success/failure. Client validation = UX; server/CF validation = authoritative. Common submit contract applies (Submitting disabled + spinner, typed errors, offline queue no-fake-success, autosave where relevant).

## Common submit contract
State Submitting disables + spinner (double-submit safe, opId). Failure → typed error (SCREEN-STATE-CONTRACT). Offline → queued+pending label. Unsaved → autosave draft (trip) or discard-confirm. Cancel/back per NAVIGATION.

## Forms list (role)
| Form | Role | Key fields | Cross-field / server rules |
|---|---|---|---|
| Login | O/D/L | identifier(email), password | auth policy; no enumeration; rate limit |
| Register driver/labourer | new | name, role(fixed D/L), phone?, email | approval=pending; role never self-elevate (server) |
| Provision owner (first run) | owner | name, email | creates org+OWNER; owner identity param |
| Add/Edit Trip | O/D | date, time, tractor(dropdown active), driver(=self/owner select), labourers(multi from active), note? | tractor/labour active+org; rateSnapshot auto; number server; ≥1 labourer optional but needed for distribution eligibility; labourer must exist |
| Tractor registry | O | name, active | add/edit/deactivate; initial Sonalika/John Deere; no hardcode logic |
| Driver/Labourer record | O | name, phone?, active, (uid link) | duplicate warn; format |
| Rate | O | amount ₹ | integer paise; >0; future-effective snapshot |
| Money rule | O | rule type + params | enum; params validated; preview; future-effective snapshot |
| Attendance correction | O | worker, date, status(work/absent), reason(required) | owner only; no silent overwrite; audit |
| Temp assignment | O/(auth D) | labourer, assignor, start/end datetime, reason, scope | end>start; scope valid; expiry server-enforced |
| Alert | O | message(optional), recipients | owner only; ack UI |
| Settings | O | summary time(19:30 in window), notif prefs, defaults | enum/range; owner; audit |
| Profile | O/D/L | displayName, phone?, photo | self content only; role/status/org immutable; photo via Storage(Blaze) |
| Export | O | date range, breakdown options | owner only; real data |

## Validation guarantees
- Cross-field/uniqueness enforced server-side (trip number, closure boundary, approvals), not only client.
- Financial fields integer paise, non-negative, range; never float.
- No fabricated/placeholder option data: tractors/labourers come from real registry/catalogue, empty states truthful.
- Every field has a test (format/required/duplicate/cross-field/server-reject).

## Focus/IME
Autofocus first required on editor screens; IME next→done; inline errors + live-region announcement (a11y); keyboard handles all actions.
