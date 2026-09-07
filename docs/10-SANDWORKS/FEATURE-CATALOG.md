# FEATURE CATALOG — SAND WORKS V1

Every required feature with role map, primary screen(s), backend-authority note, and status. Statuses: V1 REQUIRED · V1 OPTIONAL · DEFERRED · OUT OF SCOPE. No feature is vaguely described; behaviour traceability in `IMPLEMENTATION-CONTROL.md` + `09-IMPLEMENTATION`.

Legend: O=Owner, D=Driver, L=Labourer. Backend-authoritative = enforced in Security Rules/CF, never client.

| ID | Feature | Roles | Primary screen | Backend-authority note | Status |
|---|---|---|---|---|---|
| SWF-01 | Auth (OWNER/DRIVER/LABOURER sign-in) | O/D/L | Auth | Real auth; never fake | V1 REQUIRED |
| SWF-02 | User registration + approval (driver/labourer) | O(approve) D/L(reg) | Users/approvals | approval server-authoritative | V1 REQUIRED |
| SWF-03 | Owner dashboard (operational overview) | O | Owner dashboard | reads scoped by org | V1 REQUIRED |
| SWF-04 | Driver dashboard (+ ADD TRIP primary) | D | Driver dashboard | own-scope reads | V1 REQUIRED |
| SWF-05 | Labourer dashboard (read-only metrics) | L | Labourer dashboard | own-scope reads; derived from persisted trips | V1 REQUIRED |
| SWF-06 | Trip create/edit (driver + owner) | D/O | Trip editor | number CF-authoritative | V1 REQUIRED |
| SWF-07 | Tractor registry (owner-managed; init Sonalika/John Deere) | O | Tractors | owner write | V1 REQUIRED |
| SWF-08 | Labourer registry + attendance/work-day tracking | O | Labourers/attendance | corrections owner + reason + audit | V1 REQUIRED |
| SWF-09 | Rate config + per-trip snapshot (default ₹200) | O | Rates | rate & snapshot server-authoritative | V1 REQUIRED |
| SWF-10 | Money calculation + distribution (equal default; configurable) | O (config) all (view own) | Trip/reports | calc server-side deterministic; integer | V1 REQUIRED |
| SWF-11 | Daily summary/closure (19:30 configurable) | O/D/L (notify) | Scheduled | idempotent per (date,org); server-side if Blaze | V1 REQUIRED (fallback honest) |
| SWF-12 | Accrued earnings (never "payment") | O/D/L | Labourer dashboard | derived; wording locked | V1 REQUIRED |
| SWF-13 | Weekly + monthly leaderboard (top-3, real data) | all | Leaderboard | derived from trips; deterministic tie | V1 REQUIRED |
| SWF-14 | Profile (name/role/phone/picture/status/stats) | all | Profile | photo via Storage IF Blaze | V1 REQUIRED |
| SWF-15 | Owner alert/warning (operational) | O→D/L | Alert send | owner-only send; strongest-compliant | V1 REQUIRED |
| SWF-16 | Notifications A–F (role/user-targeted) | all | Notifications | CF send; no private-financial broadcast | V1 REQUIRED |
| SWF-17 | WhatsApp/today's-trip share (driver) | D | Driver share | real values; share-sheet fallback | V1 REQUIRED |
| SWF-18 | Export PDF + CSV (owner only) | O | Reports/export | owner-only; CF/storage if Blaze | V1 REQUIRED |
| SWF-19 | Settings (rates/tractors/money rules/summary time/notifs/profiles) | O | Settings | owner write | V1 REQUIRED |
| SWF-20 | Offline tolerance + idempotent sync + explicit conflict | O/D | all writes | idempotency keys; no fake success | V1 REQUIRED |
| SWF-21 | Security foundation (RBAC/org-scope/audit/rules) | all | cross | Security Rules + CF | V1 REQUIRED |
| SWF-22 | Temporary labour assignment + expiry | O (+D auth) | Assignments | expiry backend-enforced | V1 REQUIRED |
| SWF-23 | UX design system / M3 / a11y / responsive | all | all | — | V1 REQUIRED |
| SWF-24 | Profile photo via Firebase Storage | O/D/L | Profile | Storage rules; IF Blaze | V1 OPTIONAL |
| SWF-25 | Cloud-scheduled daily closure (CF) | all | Scheduled | IF Blaze; else fallback | V1 OPTIONAL |
| SWF-26 | Cloud backup (owner) | O | Settings/backup | IF Blaze | V1 OPTIONAL |
| SWF-27 | Analytics (owner, justified, no PII) | O | Dashboard | — | V1 OPTIONAL |
| SWF-28 | Dark/light theme polish | all | — | — | V1 OPTIONAL |
| SWF-90 | Public release / Google Play | — | — | — | OUT OF SCOPE |
| SWF-91 | ADMIN/delegate role | — | — | — | OUT OF SCOPE |
| SWF-92 | Legacy Flutter data migration | — | — | — | OUT OF SCOPE (start fresh) |
| SWF-93 | Labourer write capability | L | — | — | OUT OF SCOPE (read-only) |
| SWF-94 | Payment disbursement/ledger "payment completed" | — | — | accrued totals only | OUT OF SCOPE |
| SWF-95 | Fake Storage/CF on Spark | — | — | — | OUT OF SCOPE (fallback only) |

## Explicit IMPLEMENT/DEFER/BLOCK/NA map (per §11 of control brief)
- IMPLEMENT: SWF-01..23 (V1 REQUIRED) + approved V1 OPTIONAL.
- DEFER: SWF-24/25/26/27/28 until Blaze/owner approval; never silently build.
- BLOCK: any requiring env/Blaze that owner hasn't provisioned (SWF-24/25/26), and SW-BLK-1..3.
- OUT OF SCOPE: SWF-90..95.
No silent omission; every feature is IMPLEMENT/DEFER/BLOCK/OUT here.
