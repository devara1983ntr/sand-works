# Complete User Journeys

Status: Phase 0.5 (PROPOSED). Each journey: Actor, Goal, Preconditions, Entry, Steps, UI reaction, Backend, DB, Validation, Conditional, Success, Failure, Recovery, Exit. Reuses audit J1-J9 (reference) and adds net-new journeys. Full text kept compact; every step maps to a screen & backend op.

## J1 First launch (owner)
Actor: new owner. Entry: install→N-01.
Steps: splash → no account → login/create via invite? (owner provisioning is OUT-of-band server task, not self sign-up) → provisioning N-07 set profile → owner home.
Validation: email format; password policy; (owner identity bound by provisioning). Success: owner dashboard. Failure: network/disabled. Exit: home.

## J2 Returning user login
Actor: owner/driver/labourer. Entry: N-03.
Goal: reach role home. Preconditions: account exists & active.
Steps: email+password → Auth → role resolve → role home.
UI: spinner; disable double-submit. Backend: Auth + role check. Conditional: no profile → N-07; disabled → N-06; expired token → N-08. Failure: invalid creds/offline. Recovery: reset/retry.

## J3 Password recovery
Actor: any. Entry N-03 "Forgot?" → N-04 email → (deep-link) N-05 set new → login.
Validation: email; token expiry. Security: no enumeration; rate limit. Audit: account recovery event.

## J4 Account disabled / deleted
Actor: suspended user attempts login/action.
UI: N-06 notice with support. Backend: server status check blocks. No data shown. Recovery: contact owner.

## J5 Owner daily workflow (record a trip)
Actor: owner. Entry N-20 → N-24/N-25.
Goal: add a trip + attendance for today.
Steps: open today's session (or create), add trip (driver/tractor/place), add/select labourers, mark present/absent, save.
Validation: session unique; driver req; ≥1 labour; numbering server-authoritative.
Backend: create session/trip + attendance (offline queue if offline). UI: pending/syncing when offline. Success: list refresh. Audit: creation.
Recovery: draft autosave; retry on error; offline reconcile.

## J6 Owner manages crew (drivers/labourers)
Actor: owner. N-29..N-32.
Steps: list → add/edit (name, phone) → active/inactive. Validation. Backend: catalogue CRUD (rules owner). Audit on edits. Empty states.

## J7 Owner manages users & roles
Actor: owner. N-33/34.
Steps: invite user → assign role (driver/labourer link) → suspend/activate.
Backend: CF only for role/status; audit. Security: no client role change. Conditional: cannot demote/suspend the last active owner (guard). Recovery.

## J8 Owner backup & restore
Actor: owner. N-38.
Steps: export local backup / enable cloud; restore with confirm. Validation+rollback+verification (reference). Failure paths defined. Audit: restore.

## J9 Driver journey (D-1)
Actor: driver. Entry N-03→N-50.
Steps: see assigned trips → open N-51 → confirm/update status (CF) → complete → notifications. Read own only. Validation/state machine via CF. Offline: queue confirm. Audit.
Recovery: no permission → Forbidden; reassignment → live update.

## J10 Labourer journey (D-1)
Actor: labourer. Entry → N-60.
Steps: see own attendance/presence → details/history. Read-only (or confirm own if decided). No modifying others. Recovery.

## J11 Offline use & recovery
Actor: owner (write) / roles (read).
Goal: continue field entry offline.
Steps: go offline → banner → reads from cache → writes queue → reconnect → auto-sync → reconcile → success/conflict. Never fake success. Conflict resolution UI on owner conflicts. See OFFLINE-SYNC-ARCHITECTURE.

## J12 Notification-driven
Actor: driver/labourer/owner. Trigger FCM → tap → deep link → re-authorized target.
Failure: notification arrived but target inaccessible (deleted/reassigned) → friendly Forbidden/not-found with link to current list.

## J13 Error / retry
Generic: any op error → typed message → Retry re-runs idempotent op → recovery. Session-expiry mid-flow preserved.

## J14 Account deletion / self-service
Owner requests account data export; delete via CF w/ safeguards; audit; dependent data handling (never hard-delete org data). Labourer/driver self-delete (if D-1) scoped.

## Cross-cutting (each journey answers)
What user sees? what they can do? what happens on action? conditions? data change? who authorized? failure? offline? next? notification? log? test? edge cases.
The remaining specific state-handling and edge behaviour is in SCREEN-STATE-MATRIX, EDGE-CASE-AUDIT, and END-TO-END-FLOWS. Driver/labourer journeys are gated by D-1.
