# NAVIGATION — SAND WORKS

Kotlin + Navigation Compose. Role-scoped graphs; orange = active. No orphan route, no dead-end. Large screens: NavigationRail + two-pane. Every route defines entry/exit/args/auth/authz/back/deep-link.

## Auth graph
```
Splash ──(session?)──► Role home
  │ no session
  ▼
Login ──► (owner provisioning first-run) ──► Role home
  └─ Forgot/Reset ─┐
Disabled/SessionExpired screens on interception
```
After auth, any saved deep link is resolved (re-validated for role/ownership).

## Owner graph
```
Owner Home (Dashboard)            [Home tab]
 ├─ Approvals (pending registration)
 ├─ Users / Drivers / Labourers (manage)
 ├─ Tractors (registry)
 ├─ Trips ─ Trip editor
 ├─ Attendance / Corrections
 ├─ Rates / Money rules
 ├─ Leaderboard (weekly/monthly)
 ├─ Reports / Export (PDF/CSV)
 ├─ Alerts (send warning)
 ├─ Assignments (temp labour)
 ├─ Notifications
 ├─ Settings
 ├─ Audit (view)
 └─ Profile / About
```

## Driver graph
```
Driver Home (Dashboard)            [Home]
 ├─ Trips ─ + Add Trip ─ Trip editor (own)
 ├─ Assigned / Operational info
 ├─ My Totals ─ WhatsApp/share
 ├─ Notifications
 └─ Profile
```
Labourer graph:
```
Labourer Home (Dashboard, read-only)   [Home]
 ├─ Working trips / history (own)
 ├─ Weekly/Monthly leaderboard (own view)
 ├─ Notifications
 └─ Profile
```

## Navigation patterns
- Bottom nav / Rail: **Home · Work · Trips · More** (Owner). Driver: Home · Trips · More. Labourer: Home · More.
- Deep links: to a trip/assignment/approval/notification/alert target; on cold start resolve auth then ownership/existence; if stale → NotFound/Forbidden friendly fallback to Home.
- Two-pane: Owner Trips list↔editor; Reports list↔detail; Labourer history↔detail on large screens.

## Back behaviour (all roles)
- Top-bar back + system back + predictive gesture identical; pop to parent (origin preserved).
- Detail/editor back: saved-once pops immediately; unsaved new → autosave draft or discard-confirm (never silent data loss).
- Deep-link/notification back → returns to source list; session-expiry → re-auth preserving state/outbox.
- Dialogs/sheets: back dismisses; keyboard back closes IME first. No screen has an undefined Back.

## Route register (exemplar — full set in SCREEN-CATALOG + implementation)
Each route: parent → child, owner role, args (ids/date/period), auth=role claim, authz=org+ownership, back target, deep-link key. Reconciliation of all routes to task inventory is in IMPLEMENTATION-CONTROL + TRACEABILITY.
