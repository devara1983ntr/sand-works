# Functional Requirements Register (current product)

Purpose: A consolidated FR register (current + proposed). This extends the PRD summary. Requirement IDs are stable references used across the suite. Status legend per `00-AUDIT-INDEX.md`.

Status: VERIFIED unless labelled. Commit `2dd2fe4`.

## Current functional requirements
| FR-ID | Requirement | Status | Key evidence |
|---|---|---|---|
| FR-01 | Launch → splash → auto dashboard after ~1.5 s | VERIFIED | `splash_screen.dart` |
| FR-02 | Dashboard auto-loads current date + auto session | VERIFIED | `dashboard_screen.dart`, `date_time_utils.dart` |
| FR-03 | Session = 04:00–11:59 Morning, else Evening | VERIFIED (code) | `getCurrentSession` |
| FR-04 | Create Work+Trip with validation (work type, driver req.; ≥1 labour) | VERIFIED | `add_edit_work_screen.dart` |
| FR-05 | Editing preserves date/session isolation; reject `editingTrip` w/o `editingWork` | VERIFIED | initState throw |
| FR-06 | Deterministic Work id by date+session (`work_<date>_<session>`) | VERIFIED | `_saveWork` |
| FR-07 | Auto trip number across morning+evening | VERIFIED | usecase + test |
| FR-08 | Copy last-trip context for next trip | VERIFIED | `work_bloc.dart` |
| FR-09 | Save trip + labour attendances; soft-mark removed labours absent | VERIFIED | `_onSaveFullWorkTrip` |
| FR-10 | Dashboard search (driver/tractor/#/type/place) | VERIFIED | `work_bloc.dart` |
| FR-11 | Filter dashboard | PARTIAL (state-only, unreachable UI) | `FilterDashboardEvent` |
| FR-12 | Delete latest trip (confirm) | VERIFIED | dashboard |
| FR-13 | Delete specific trip (confirm + cascade) | VERIFIED | dashboard/details |
| FR-14 | Trip details: add/edit/remove labour; toggle attendance; undo-remove | VERIFIED | `trip_details_screen.dart` |
| FR-15 | History grouped date→session; edit/delete/drill | VERIFIED | `history_screen.dart` |
| FR-16 | Analytics KPIs + sortable table | VERIFIED | `analytics_screen.dart` |
| FR-17 | Backup `.labourbackup` ≤25 MB via SAF | VERIFIED | `settings_screen.dart` |
| FR-18 | Restore validate + snapshot rollback + count verify | VERIFIED | `settings_screen.dart` |
| FR-19 | Draft autosave + restore | VERIFIED | `add_edit_work_screen.dart` |
| FR-20 | All UI strings English & hardcoded | VERIFIED | all screens (no ARB) |
| FR-21 | No network/permission dependencies | VERIFIED | AndroidManifest (no INTERNET) |

## Proposed functional requirements (native) — PROPOSED
| FR-ID | Requirement |
|---|---|
| PFR-01 | Owner/admin account represented server-side (UID → role), never hardcoded credentials |
| PFR-02 | Driver & labourer roles with server-enforced capability sets |
| PFR-03 | Role-scoped home/dashboards (owner, driver, labourer) |
| PFR-04 | Job creation & assignment by owner; driver acceptance/completion |
| PFR-05 | Attendance recorded server-authoritatively; offline queue + reconcile |
| PFR-06 | Notifications via FCM + Cloud Functions (role-scoped) with deep links |
| PFR-07 | Reports, payments ledger only if product justifies |
| PFR-08 | Audit logs of privileged/admin actions |
| PFR-09 | Backup: optional cloud backup + retain local `.labourbackup` export |

> All PFRs require approval; none are validated against real users yet.
