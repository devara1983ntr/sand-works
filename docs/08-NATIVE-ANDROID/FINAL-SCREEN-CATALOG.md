# FINAL SCREEN CATALOG — V1 (Phase 0.75)

Scope: S-V1 owner-only. Every V1 screen is specified. Common behaviour is factored into a **Shared Screen Contract (SSC)** below; per-screen rows list only deltas and pointers. No critical V1 screen is undefined.
Driver/labourer screens (N-50..N-63) are DEFERRED (D-1) and deliberately absent from V1.

## Shared Screen Contract (SSC) — applies unless overridden
- App bar: contextual title; back shown when there is a parent; top-bar action icons only where the role may act.
- Back behaviour per FINAL-NAVIGATION §Back: system back + top-left back + predictive gesture all consistent; unsaved changes → autosave-draft (new) or keep (saved); destructive never on plain back.
- Bottom navigation: Owner shell only (Dashboard, Work/Trips, Reports, More) on compact; M3 Rail on medium+; re-selecting active tab scrolls to top.
- States (per FINAL-UI-STATE-CONTRACT): Loading skeleton → Success/Empty/Error/Offline; Submitting disabled + spinner; partial failure handled per-screen; Forbidden/Unauthorized distinct; Session-expiry preserves drafts.
- Unauthorized = not signed in → re-auth; Forbidden = signed in but no permission → message + allowed area.
- Gestures: no novelty; every gesture has an a11y alternative (FINAL-GESTURES).
- Analytics: screen_view on entry; typed error events; no PII.
- Security/perf/test: cross-cutting contracts apply to every screen.
- Empty/offline/error copy: per EMPTY-STATE-MATRIX / ERROR-STATE-MATRIX / OFFLINE-STATE-MATRIX unless overridden.

## V1 screen set (all MUST unless noted)
| Screen | Purpose | Role/Auth | Route (parent→child) | Content/components | Primary/Secondary actions | Key states & behaviours |
|---|---|---|---|---|---|---|
| N-01 Splash | readiness + route to login/home | all; no role yet | root | logo, progress | — | Loading→(session?) home : login. App-check token. |
| N-03 Login | owner sign-in | unauth→OWNER | root→(N-10) | email, password, forgot | Sign in / forgot | Auth/network/rate/disabled/expiry per FINAL-ERROR. |
| N-04 Forgot password | send reset | unauth | N-03→N-04→(N-05 via link) | email, submit | Send | no enumeration; success notice. |
| N-05 Reset password | set new pw | reset-token | deep link→N-05 | new pw + confirm | Save | token expiry; policy. |
| N-06 Account disabled/notice | inform | any | N-03/redirect | message, support | Contact | no data shown. |
| N-07 Provisioning (first run) | bind owner identity + org | unauth→OWNER | N-03→N-07 | name, accept | Provision | CF create org+user; single account. |
| N-08 Session expired | re-auth | OWNER | intercepted→N-08→N-03 | message, sign-in | Retry | preserves drafts/outbox. |
| N-10 Owner shell | container nav | OWNER | N-03→N-10 | bottom nav/Rail: Dashboard, Work, Reports, More | nav | role gating (owner only). |
| N-20 Dashboard (today) | today's sessions/trips + search + add | OWNER | N-10→N-20 | today list, session filter, search, KPI mini, FAB + | +new work; search | Loading/Empty/Offline/Error; pull-refresh. |
| N-22 Day/Session detail | a day's sessions + close | OWNER | N-20/27→N-22 | sessions (Morning/Evening), trips, close action | close; open | empty/offline; close confirm+audit. |
| N-23 Trip detail + attendance | view/edit trip; attendance | OWNER | N-20/22/25→N-23 | trip info, labour list toggles, add/remove labour (undo), status | save attendance; edit | partial-failure; correction reason; conflict(rev). |
| N-24 New/Edit Work Session | create/edit date+session | OWNER | N-20/22→N-24 | date picker, session segmented, workType, place, status | Save/Cancel | draft autosave; uniqueness; duplicate handling. |
| N-25 Trip editor / next-trip | add/edit trip + select labour | OWNER | N-24/23→N-25 | tractor/vehicle, driver(record), place, notes, number(auto), labour roster, "next trip" copy | Save next/cancel | validation; draft; numbering auto; offline queued. |
| N-27 Reports/Analytics | KPIs + sortable table + grouped history + search/filter | OWNER | N-10→N-27 | KPI cards, data table sortable, period, export | export CSV (S1); drill | empty/offline/export progress. |
| N-28 Report/export detail (S1) | CSV/export result | OWNER | N-27→N-28 | file actions | download | progress/cancel/error. |
| N-29 Labourers catalogue | manage labourers | OWNER | More→N-29 | list, add/edit/soft-delete, active toggle | add; edit | confirm on remove; undo; audit. |
| N-30 Vehicles/Tractors (S2) | vehicle master | OWNER | More→N-30 | list CRUD | add/edit | soft-delete. |
| N-31 Drivers catalogue | manage driver records | OWNER | More→N-31 | list CRUD + vehicle | add/edit | confirm/audit. |
| N-36 Audit log viewer (S5) | view owner-privileged audit | OWNER | More→N-36 | log list, filters, before/after | refresh | read-only; empty. |
| N-37 Settings (business) | defaults/flags | OWNER | More→N-37 | workType defaults, hints, session display, report flags | Save | validate enums/ranges; audit changes. |
| N-38 Backup/restore | secure backup + cloud | OWNER | More→N-38 | local/cloud toggles, last backup, backup now, restore list | Backup now; Restore | progress/cancel; rollback; verify. |
| N-39 Account & security | password/delete-data/export | OWNER | More→N-39 | change pw, export my data, delete data, sign out | actions | destructive confirm + re-auth. |
| N-40 Owner profile | name/phone/photo | OWNER | More→N-40 | avatar, name, phone | Save | photo upload; validation. |
| N-41 Owner notification centre (S4) | system events | OWNER | More→N-41 | events list, read state, deep link | open | offline read; empty. |
| N-42 Help/About (S3) | FAQ/version/support | OWNER | More→N-42 | content, version | support link | replaces inert reference tiles. |

Every row's Back, states, validation, gestures, a11y, analytics, security, backend, DB, tests are defined by SSC + the cross-referenced FINAL contracts (ERROR / UI-STATE / NAVIGATION / FORMS / GESTURES / RBAC / DATABASE / BACKEND / TEST). Where a row needs overrides they are noted inline (e.g., N-23 partial-failure; N-38 rollback; N-39 destructive re-auth).

## Excluded screens (DEFERRED, D-1)
N-11/12 role shells (driver/labourer), N-50..53 driver home/trips, N-60..63 labourer attendance. Not in V1.

## Verification
Source of V1 screen set: Phase-0.5 COMPLETE-SCREEN-INVENTORY + SCREEN-BY-SCREEN + MISSING-SCREENS, reconciled to S-V1. Driver/labourer gaps are DEFERRED, not silently dropped.
