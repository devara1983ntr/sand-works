# Product Reconciliation — Flutter reference → Native Android product

Status: Phase 0 reconciliation. Source: forensic audit (`00-AUDIT-INDEX.md`), code evidence, and the native product brief. Commit audited `2dd2fe4`.
Classification: KEEP / IMPROVE / REPLACE / REMOVE / NEW / DEFER / UNVERIFIED. Open items are tagged **DECISION REQUIRED (D-xx)** per the Phase-0 rule (never silent ambiguity).

---

## 1. The three truths must stay separate
- **Flutter reference tells us:** what exists today — an offline, single-user, no-auth labour/trip recorder.
- **Product brief tells us:** what the native product should accomplish — a labour/workforce management app with Owner/Admin, Driver, Labourer roles and a secure backend.
- **Native architecture (this phase) defines:** how it should be built safely — reconciled below.

Do not mechanically translate Flutter code. Reconcile intent, then design.

## 2. Reconciled product definition (PROPOSED baseline)
**Labour Party (native)** = a labour/workforce & trip-management application for a **single-owner contracting business** (owner: Ramesh Sahu). The owner (and staff admins) records a day's **work sessions**, each containing **trips** (tractor + driver) and **labour attendance**; the system keeps authoritative, secure, cloud-backed records while remaining **usable offline in the field**, and (optionally) lets **drivers and labourers** log in to see their own assigned trips / attendance.

Scope boundary: labour/workforce operations only. No payroll/billing engine in v1 (payments DEFER). No unrelated features.

## 3. Reconciliation of core domain (existing → native)
| Flutter entity/behaviour | Evidence | Native mapping | Class |
|---|---|---|---|
| Work (date + session + workType + place) | `work.dart` | `WorkSession` (daily unit the owner opens/closes) | IMPROVE |
| Trip (tractor, driver, tripNumber, notes, time) | `trip.dart` | `Trip` under a WorkSession; tractor/driver become references (master) not free text | IMPROVE |
| Sequential trip numbering (morning/evening, per date) | `CalculateNextTripNumberUseCase` | Keep as server-enforced use case/rule | KEEP (move to backend authority) |
| Labour master list (name, optional phone) | `labour.dart` | `Labourer` catalogue (org-owned); optional link to a login account | IMPROVE |
| Driver as free-text name on trip | `trip.driverName` | `Driver` catalogue (org-owned) + optional user account link; trip references driver | REPLACE (free-text → master + picker) |
| Per-trip attendance present/absent | `TripLabour` | `Attendance` (server-authoritative, auditable, with history) | KEEP / IMPROVE |
| Dashboard today (summary, quick-add next trip) | `dashboard_screen.dart` | Owner dashboard | IMPROVE |
| Search today + filter (partial) | bloc events | Full search/filter UI + history-wide search | IMPROVE (finish what Flutter left partial) |
| History grouped date/session | `history_screen.dart` | History/archives screen | KEEP |
| Analytics KPIs + sortable table | `analytics_screen.dart` | Reports (owner) | IMPROVE |
| Local `.labourbackup` backup/restore | `settings_screen.dart` | Keep portable export (improved/encrypted) + cloud backup | IMPROVE |
| Draft autosave | `draft_model.dart` | Local drafts (Room/DataStore) | KEEP |
| Offline-first local (Hive) | Hive boxes | Hybrid: Room cache + Firestore offline persistence (cloud-authoritative) | REPLACE (see OFFLINE-SYNC) |
| Single local user, no auth | whole app | Authenticated roles | REPLACE |
| No remote backend | manifest (no INTERNET) | Firestore + Auth + Functions + FCM | NEW |

## 4. Feature-class reconciliation
| Feature | Flutter | Native class | Notes |
|---|---|---|---|
| Log daily work/trips | F-06..F-09 | KEEP/IMPROVE | owner-authoritative; offline queue |
| Labour & attendance | F-10..F-12 | KEEP/IMPROVE | auditable; server rule enforced |
| History/analytics | F-15/F-16 | KEEP/IMPROVE | → Reports |
| Backup/restore | F-17/F-18 | IMPROVE | encrypted + cloud |
| Auth | none | NEW | required |
| Roles/RBAC | none | NEW | required (Driver/Labourer) |
| Owner account (Ramesh Sahu) | absent from code | NEW (provisioning, no hardcoded creds) | required |
| Driver self-service (see assignments) | none | NEW (scope dependent) | D-1 |
| Labourer self-service attendance | none | NEW (scope dependent) | D-1 |
| Job assignment & status workflow | none | NEW (owner→driver assignment is a scope decision) | D-1/D-6 |
| Notifications | none | NEW | Phase later |
| Reports service / payments | none (light analytics) | DEFER | payments out of v1 |
| Multi-organization/multi-owner | none | DEFER | single-org v1 (D-2) |
| Tractor/vehicle master | free text chips | IMPROVE | master list |
| Web/desktop companion | none | DEFER | native core only |
| Live map/GPS trip tracking | none | DEFER | not evidenced by product need |

## 5. Screen reconciliation (existing → native)
Every Flutter screen becomes a native screen spec in `NATIVE-NAVIGATION.md`. Summary mapping: Splash→AuthGate/Splash; Dashboard→Owner Home; Trip Details→Trip detail (attendance editor); Add/Edit Work→Work session / trip editor; Confirm Next Trip→copy-next-trip confirm; History→History; Analytics→Reports; Settings→Settings & Account; `/details` orphan→remove or become owner day-detail.

## 6. Explicit removals / don't-carries
- REMOVE: the orphan `/details` route + inert search/filter controls (BUG-02/03/04).
- REMOVE: free-text driver/tractor typos (replaced by catalogues + pickers).
- REMOVE: offline-first-as-only-mode architecture (becomes hybrid with cloud authority).
- REMOVE/DEFER: `glassmorphism` heavy decoration (native design system).
- DEPRECATED (flag): duplicate `LabourFormModel`, duplicate `@override`, dead switch branches — do not reproduce.

## 7. Evidence gaps / UNVERIFIED
- Whether real production data exists in any deployed Flutter install → affects MIGRATION-STRATEGY (D-3).
- Owner identity "Ramesh Sahu" vs package id `com.roshan.labourparty` → D-4.
- Session-boundary semantics (code 04:00 vs PRD text 00:00) → D-5.
- Whether drivers/labourers operate their own devices at all → D-1.

## 8. Decision register (Phase 0)
| ID | Question | Recommended | Status |
|---|---|---|---|
| D-1 | Do DRIVER and LABORER roles log in via their own app in v1, or are they owner-managed records only (with role model prepared)? | Owner runs core; DRIVER self-service optional; LABORER self-service likely DEFER. Role + data model prepared either way. | DECISION REQUIRED (business/ops) |
| D-2 | Single organization vs multi-tenant? | Single-owner org in v1; multi-tenant deferred | DECISION REQUIRED |
| D-3 | Migrate existing Hive/.labourbackup production data? | Assume none unless told; provide export→import path if needed | DECISION REQUIRED |
| D-4 | Final product/package identity & owner branding | Resolve before release | DECISION REQUIRED |
| D-5 | Session boundary value | Follow product intent; fix inconsistency | DECISION REQUIRED |
| D-6 | Job "assignment & status to driver" granularity | Owner-centric trips + attendance; driver assignment UI later | DECISION REQUIRED |
| D-7 | Reports scope & KPIs v1 | Owner reports (trips, attendance, driver/labourer summaries) | PROPOSED (confirm) |
| D-8 | Retention/compliance | Not inventing legal; flag for confirmation | DECISION REQUIRED |

## 9. Verification
Reconciliation grounded in code/audit (VERIFIED) for existing behaviour; native product targets are PROPOSED and tied to D-1..D-8. No implementation performed.
