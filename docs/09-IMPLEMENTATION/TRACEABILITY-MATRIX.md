# SAND WORKS — Requirements Traceability Matrix

Status: **RECONCILED to `docs/10-SANDWORKS/FEATURE-CATALOG.md`, `SCREEN-CATALOG.md`, `BACKEND-OPERATIONS.md`.** Supersedes retired S-V1 trace. Planning only.

Direction 1: every feature row below maps → screen(s) → implementing tasks → phase/gate, so no final requirement is orphaned.
Direction 2: every SW-xxx task maps back to ≥1 SWF row + screen + rule/data/security + test (each `tasks/PHASE-*.tasks.md` contract carries this). A task with no real requirement does not exist (all 52 map below or to the phase's shared infra rows).

## Feature → Screen → Task map (V1-REQUIRED SWF-01..23)
| Feature | Screen(s) (SCREEN-CATALOG) | Tasks | Phase/Gate |
|---|---|---|---|
| SWF-01 Auth sign-in | O/D/L sign-in, splash, About | SW-301, 302, 305 | P3/G3 |
| SWF-02 Registration+approval | O-Approvals; D/L register | SW-303, 304, 503, 601/701 | P3/P5/P6/P7 |
| SWF-03 Owner dashboard | O-Home/Dashboard | SW-502, 507 | P5/G5 |
| SWF-04 Driver dashboard (+ADD TRIP) | D-Home/Dashboard, Trip editor | SW-602, 603 | P6/G6 |
| SWF-05 Labourer read-only metrics | L-Home/Dashboard | SW-702, 703 | P7/G7 |
| SWF-06 Trip create/edit (D/O) | Trip editor, Trip list/detail | SW-603, 307(B-04/05), 108 | P6 (+P3/G3) |
| SWF-07 Tractor registry | O-Tractors; D tractor select | SW-504, 603 | P5/P6 |
| SWF-08 Labourer registry+attendance | O-Labourers, Attendance | SW-504, 505, 307(B-06) | P5/G5 |
| SWF-09 Rate config + snapshot (₹200) | O-Settings/Rates | SW-401, 506, 307(B-07) | P4/P5 |
| SWF-10 Money calc + distribution | Trip/reports; O-Rule config | SW-402, 403, 506, 108 | P4/G4 |
| SWF-11 Daily summary/closure (19:30) | scheduled; O/D/L summary | SW-403, 307(B-09) | P4/G4 (+G8 notify) |
| SWF-12 Accrued earnings (wording) | L metrics; D/O totals | SW-403, 702, 604, 704 | P4/P7/G4 |
| SWF-13 Weekly+monthly leaderboard | L/O leaderboard | SW-404, 703, 307(B-10) | P4/G4 |
| SWF-14 Profile | O/D/L Profile | SW-605, 704, 307(B-16) | P6/P7 |
| SWF-15 Owner alert/warning | O-Alert send; D/L alert | SW-803, 307(B-13/14) | P8/G8 |
| SWF-16 Notifications A–F | O/D/L notification centre | SW-801, 802, 803, 307(B-13) | P8/G8 |
| SWF-17 WhatsApp/today share (D) | D-My totals/share | SW-604, 307(B-18) | P6/G6 |
| SWF-18 Export PDF+CSV (O) | O-Reports/export | SW-508, 307(B-15) | P5/G5 |
| SWF-19 Settings (O) | O-Settings | SW-507, 506, 401/402 | P5 |
| SWF-20 Offline+idempotency+conflict | all write screens | SW-201..204 | P2/G2 |
| SWF-21 Security foundation | all | SW-301..309 | P3/G3 |
| SWF-22 Temp labour assignment+expiry | D assign; O manage | SW-307(B-11/12), 603, 505 | P3/P6 |
| SWF-23 UX DS/M3/a11y/responsive | all | SW-103, 104, 108, 902 | P1/P9 |

## Optional in-scope (V1 OPTIONAL SWF-24..28)
| Feature | Tasks | Phase/Gate | Blocker |
|---|---|---|---|
| SWF-24 Profile photo via Storage | SW-605, 704, 307(B-16) | P6/P7 | SW-BLK-2 (Blaze) |
| SWF-25 Cloud-scheduled closure | SW-403 | P4/G4 | SW-BLK-2 (Blaze; else honest fallback) |
| SWF-26 Cloud backup (O) | SW-507 | P5 | SW-BLK-2 |
| SWF-27 Analytics (O, no PII) | SW-502 | P5 | SW-BLK-1 |
| SWF-28 Dark/light polish | SW-104 | P1/P9 | — |

## Out of scope (explicitly NOT traced — do not build)
SWF-90 public/Play release · SWF-91 ADMIN/delegate · SWF-92 legacy data migration · SWF-93 labourer write · SWF-94 payment disbursement/ledger · SWF-95 fake Storage/CF on Spark. No task maps to these; presence of such a task would be a defect.

## Task → Requirement (inverse, exemplar; full list in each Phase tasks file)
| Task | Maps to (feature / screen / rule / data / security / test) |
|---|---|
| SW-108 | SWF-10/13 money domain; MONEY-ENGINE-SPEC; tests |
| SW-203 | SWF-20; OFFLINE-SYNC-SPEC; CONCURRENCY-IDEMPOTENCY-AUDIT; FT-OFFLINE/FT-CONC |
| SW-306 | SWF-21; SECURITY-RBAC; DATA-MODEL; FT-RULES |
| SW-307 | SWF-06/07/08/09/10/11/13/15/16/17/18/22/24 + B-01..18; FT-CF |
| SW-403 | SWF-11/12; MONEY-ENGINE-SPEC; SCHEDULING; FT-closure idempotency |
| SW-603 | SWF-06/07/22; SCREEN-CATALOG D-Trip; FORMS-VALIDATION; FT-trip |
| SW-803 | SWF-15; NOTIFICATION-ALERT-SPEC; FT-alert |
… every other SW-xxx maps analogously (enforced in the task contract files).
