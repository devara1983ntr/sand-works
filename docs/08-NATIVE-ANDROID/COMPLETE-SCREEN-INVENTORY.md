# Complete Screen Inventory

Status: Phase 0.5 (PROPOSED + VERIFIED). Sources of truth per §1: repository (Flutter reference) → audit docs → architecture → reasoned recommendations. Screen IDs are new native IDs. Role/decision flags: D-1..D-8 (see PRODUCT-RECONCILIATION.md). Nothing is implemented.

## 1. Source classification of every screen type
| Type | Meaning |
|---|---|
| EXISTING (reference) | present as a Flutter screen (audit SCREENS.md S-01..S-09) |
| ADAPTED | carries over behaviour into native |
| NEW | required by product but no Flutter equivalent |
| MISSING | needed but not yet defined anywhere |
| ORPHAN/DEAD | reachable in Flutter only by route, not nav (e.g. `/details`) |
| ROLE-SPECIFIC | rendered per role |
| STATE-SPECIFIC | a distinct view for a state (loading/empty/error/etc.), often sub-states of a screen |

## 2. Native screen inventory (grouped)
### A. Auth & lifecycle
| N-ID | Screen | Type | Roles | Notes |
|---|---|---|---|---|
| N-01 | Splash/Launch gate | ADAPTED (S-01) | all | checks session → auth or home |
| N-02 | Onboarding / first-run explainer | NEW | all | only if product wants it (REQUIRES DECISION) |
| N-03 | Login | NEW | all | email/password |
| N-04 | Password reset request | NEW | all | |
| N-05 | Password reset confirm (deep-link) | NEW | all | |
| N-06 | Account disabled notice | STATE (within auth) | all | shown instead of home |
| N-07 | Provisioning / invite acceptance | NEW | new users | role/profile setup |
| N-08 | Session expired / re-auth | STATE | all | |

### B. Shared shells
| N-ID | Screen | Roles |
|---|---|---|
| N-10 | Owner Main shell (bottom destinations) | OWNER/ADMIN |
| N-11 | Driver shell | DRIVER (if D-1) |
| N-12 | Labourer shell | LABORER (if D-1) |

### C. Owner/admin destinations
| N-ID | Screen | Type | Notes |
|---|---|---|---|
| N-20 | Owner Dashboard | ADAPTED (S-02) | today's sessions summary + quick actions |
| N-21 | Work / Trips list (date picker) | ADAPTED (S-03/S-08) | browse a date |
| N-22 | Work Session detail | NEW (refactor of `/details` intent) | day detail w/ sessions |
| N-23 | Trip detail + attendance editor | ADAPTED (S-07) | labour present/absent |
| N-24 | New/Edit Work Session | ADAPTED (S-08) | |
| N-25 | New Trip / Copy-next-trip confirm | ADAPTED (S-08/S-09) | |
| N-26 | Attendance overview (session/day) | NEW | roll-up; role-filter |
| N-27 | Reports | ADAPTED (S-05) | KPIs + tables |
| N-28 | Report detail / export | NEW | |
| N-29 | Crew → Drivers list | NEW (from labour mgmt) | |
| N-30 | Driver detail / edit | NEW | |
| N-31 | Crew → Labourers list | ADAPTED (labour master) | |
| N-32 | Labourer detail / edit | NEW | |
| N-33 | Users & roles management | NEW | owner/admin |
| N-34 | User detail / role edit | NEW | |
| N-35 | Announcements / notifications manager | NEW | owner send |
| N-36 | Audit log viewer | NEW | owner/admin |
| N-37 | System/business settings | NEW | session boundary, defaults, retention |
| N-38 | Backup & restore | ADAPTED (S-06) | |
| N-39 | Account & security settings | NEW | |

### D. Shared/utility (any role)
| N-ID | Screen | Roles |
|---|---|---|
| N-40 | Profile view/edit | all |
| N-41 | Notification centre (list) | all |
| N-42 | Help / support / about | all |
| N-43 | Generic error / offline / recovery | all (state) |

### E. Driver (if D-1 self-service)
| N-ID | Screen |
|---|---|
| N-50 | Driver home (assigned trips) |
| N-51 | Driver trip detail (view, confirm/status via CF) |
| N-52 | Driver attendance/history |
| N-53 | Driver profile |

### F. Labourer (if D-1 self-service)
| N-ID | Screen |
|---|---|
| N-60 | Labourer home (own attendance) |
| N-61 | Labourer attendance detail |
| N-62 | Labourer history |
| N-63 | Labourer profile |

## 3. State-specific sub-screens/views (not separate routes)
Every route has sub-views: Loading, Empty, Error, Offline, Unauthorized, Forbidden, Session-expired, Success, Confirmation, Submitting. These are captured per screen in SCREEN-STATE-MATRIX.md and are NOT double-counted as routes.

## 4. Existing vs missing/orphan counts
- Existing Flutter screens mapped: S-01..S-09 (all covered).
- Orphan: `/details` (details_screen) → ADAPTED into N-22 or dropped (remove).
- Duplicate concepts: "Add Work" vs "Confirm next trip" merge into N-24/N-25 single flow.

## 5. Inaccessible/hidden-feature discovery
| Hidden capability | Has a screen? | Note |
|---|---|---|
| Draft autosave/restore | N-24 substate | reference behaviour kept |
| Backup/restore error dialogs | N-38 substates | kept |
| Pull-to-refresh | gestures on lists | |
| Deep links | N-50/N-23 via notification | new |

## 6. Verification
Inventory VERIFIED against reference screens S-01..S-09; native screens PROPOSED; role screens gated by D-1; onboarding/help gated by business decisions.
