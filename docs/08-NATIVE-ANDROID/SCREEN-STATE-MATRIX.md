# Screen State Matrix

Status: Phase 0.5 (PROPOSED). Every screen must define each state that applies. Legend: ✅ defined · ○ not applicable for this screen · ⚠ must-be-decided. Full state handling lives here; per-screen narrative in SCREEN-BY-SCREEN-SPECIFICATION.md.

## Common states (apply to most screens)
Initial · Loading · Success · Empty · Error · Offline · Refreshing · Unauthorized · Forbidden · SessionExpired · PartialFailure · Submitting · SuccessWithStaleData · Retry.

## Matrix (Owner/Admin screens)
| Screen | Loading | Empty | Error | Offline | Success | Submitting | Forbidden | Partial | Notes |
|---|---|---|---|---|---|---|---|---|---|
| N-01 Splash | ✅ | ○ | ✅(no session) | ✅ | ✅ | ○ | ○ | ○ | readiness-gated |
| N-03 Login | ✅ | ○ | ✅ | ✅ | ✅→route | ✅ | ○ | ○ | disable-account state separate |
| N-20 Dashboard | ✅ | ✅(no work) | ✅ | ✅ | ✅ | ✅(record) | ✅(driver-visible? n/a) | ✅ stale | pull-refresh = Refreshing |
| N-22 Day detail | ✅ | ✅ | ✅ | ✅ | ✅ | ○ | ✅ | ✅ | session open/closed |
| N-23 Trip detail | ✅ | ✅(no labour) | ✅ | ✅ | ✅ | ✅(attendance) | ✅ | ✅ | corrections audited |
| N-24/25 Work editor | ✅ | ○ | ✅(conflict) | ✅ | ✅→pop | ✅ | ✅ | ✅ | draft autosave state |
| N-26 Attendance overview | ✅ | ✅ | ✅ | ✅ | ✅ | ○ | ✅ | ✅ | roll-up |
| N-27 Reports | ✅ | ✅ | ✅ | ✅ | ✅ | ○ | ✅ | ✅ | export sub-op |
| N-28 Report detail | ✅ | ✅ | ✅ | ✅ | ✅ | ○ | ✅ | ○ | |
| N-29..32 Crew mgmt | ✅ | ✅ | ✅ | ✅(read) | ✅ | ✅ | ✅ | ✅ | writes online/owner |
| N-33 Users/roles | ✅ | ✅ | ✅ | ✅(read) | ✅ | ✅ | ✅(role change) | ✅ | writes via CF/owner |
| N-35 Announcements | ✅ | ✅ | ✅ | ✅(read) | ✅ | ✅(send) | ✅ | ✅ | send CF |
| N-36 Audit log | ✅ | ✅ | ✅ | ○(server) | ✅ | ○ | ✅ | ○ | read-only |
| N-37 Settings | ✅ | ○ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | |
| N-38 Backup/restore | ✅ | ○ | ✅ | ✅(partial) | ✅ | ✅ | ✅ | ✅ | restore rollback |
| N-39 Account/security | ✅ | ○ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | re-auth for sensitive |

## Driver screens (D-1)
| Screen | Loading | Empty | Error | Offline | Success | Submitting | Forbidden | Notes |
|---|---|---|---|---|---|---|---|---|
| N-50 Driver home | ✅ | ✅(no assigned) | ✅ | ✅ | ✅ | ○ | ✅ | read own |
| N-51 Trip detail | ✅ | ✅ | ✅ | ✅ | ✅ | ✅(confirm via CF) | ✅ | cannot edit others |
| N-52 Driver history | ✅ | ✅ | ✅ | ✅ | ✅ | ○ | ✅ | own only |
| N-53 Profile | ✅ | ○ | ✅ | ✅ | ✅ | ✅ | ✅ | self only |

## Labourer screens (D-1)
| Screen | Loading | Empty | Error | Offline | Success | Submitting | Forbidden | Notes |
|---|---|---|---|---|---|---|---|---|
| N-60 Labourer home | ✅ | ✅ | ✅ | ✅ | ✅ | ○ | ✅ | own attendance |
| N-61 Attendance detail | ✅ | ✅ | ✅ | ✅ | ✅ | ○ | ✅ | read own; cannot modify |
| N-62 Labourer history | ✅ | ✅ | ✅ | ✅ | ✅ | ○ | ✅ | own |
| N-63 Profile | ✅ | ○ | ✅ | ✅ | ✅ | ✅ | ✅ | self |

## State completeness rule
A screen is incomplete unless: Loading, Success, Empty (where lists), Error (with retry), Offline (where network), and (where privileged) Forbidden/Unauthorized are defined. Cross-cutting details in ERROR-STATE-MATRIX, LOADING-STATE-MATRIX, EMPTY-STATE-MATRIX, OFFLINE-STATE-MATRIX.

## Gaps flagged
- G1: No screen currently defines a distinct **Unauthorized vs Forbidden** visual beyond message → unify into one component (PROPOSED) to avoid dead states.
- G2: **SessionExpired mid-edit** must preserve drafts (all form screens) — define as shared behaviour.
- G3: **PartialFailure** (some attendance saved, later ones fail) needs explicit handling on N-23/N-26.
- Verification: PROPOSED; pending D-1 for driver/labourer rows.
