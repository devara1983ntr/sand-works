# User Personas

Status: current personas VERIFIED; future roles PROPOSED. Commit `2dd2fe4`.

## Current personas (VERIFIED)
The current app models **one user type** only — there is no auth or role layer.

### P1 — The Operator / Contractor (only current persona)
- **Who:** A labour/transport contractor or operator running daily tractor trips (sand/soil/stone) with a small rotating crew of drivers and labourers (region: India, e.g., Odisha — inferred from product context; not coded).
- **Goals:** Rapidly log each trip + attendance while on site; never lose the ledger; reconstruct a day or a period; move data to a new phone.
- **Pain points:** Manual registers; duplicate/`Labour 1` entries; orphan records; data loss on app uninstall/phone change.
- **App behaviour used:** Dashboard quick-add next trip (copy last trip), confirm-next-trip, history, analytics, backup/restore.
- **Auth:** none. They are implicitly the sole owner of their device data.
- **Evidence:** `dashboard_screen.dart`, `add_edit_work_screen.dart`, `confirm_next_trip_screen.dart`, `settings_screen.dart`.

## Future/proposed personas (PROPOSED — not in current code)
> These map to the migration brief's OWNER / DRIVER / LABOURER framing. **None exist in the current repository.**

### P2 — Owner / Admin ("Ramesh Sahu" in the brief)
- **Who:** Business owner who configures and administers the system.
- **Needs:** full dashboard, manage drivers/labourers, assign work, view reports/attendance/payments, configure the business, audit activity.
- **Security requirement:** owner identity must be represented by **backend-authoritative role/UID**, never by hardcoded client credentials. See `04-SECURITY/AUTHENTICATION-AUTHORIZATION.md`.
- **Note:** The name "Ramesh Sahu" is **not found in the codebase**; package id uses "roshan". Resolve identity before branding/native naming (OQ-2).

### P3 — Driver
- **Who:** tractor driver performing assigned trips.
- **Needs:** view assigned work/trips, update trip status, confirm attendance/completion, see notifications.
- **Forbidden:** manage users, alter master work definitions, change their own wage/records.

### P4 — Labourer / Worker
- **Who:** daily-wage worker.
- **Needs:** view attendance/pay status for the trips they worked; receive notifications.
- **Forbidden:** admin/modify records.

## Persona → role mapping summary
| Persona | Current | Native (PROPOSED) |
|---|---|---|
| P1 Operator | = the whole app | ≈ Owner/Admin |
| P2 Owner | n/a | Owner/Admin |
| P3 Driver | driver is a string field | Driver role |
| P4 Labourer | Labour entity + attendance bool | Labourer role |

## Verification status
VERIFIED: P1 and the absence of other roles. PROPOSED: P2–P4. Open: OQ-1 (single-operator assumption), OQ-2 (owner identity).
