# Missing / Orphaned / Dead Screens

Status: Phase 0.5 (PROPOSED + VERIFIED). Purpose: surfaces screens that don't exist yet or shouldn't exist. This is a discovery doc, not an implementation list.

## 1. Reference screens discovered (VERIFIED from Flutter)
| Flutter screen | Status | Native disposition |
|---|---|---|
| S-01 Splash | Existing | → N-01 |
| S-02 Dashboard | Existing | → N-20 |
| S-03 Details (`/details`) | **ORPHAN** (route-only, no nav reach) | → redesign as N-22 or remove (recommend remove/replace) |
| S-04 History | Existing | → History within Reports/N-27 or dedicated History |
| S-05 Analytics | Existing | → N-27 Reports |
| S-06 Settings | Existing | → N-38/N-39/N-37 split |
| S-07 Trip Details | Existing | → N-23 |
| S-08 Add/Edit Work | Existing | → N-24/N-25 |
| S-09 Confirm Next Trip | Existing | → N-25 |
| (none) Login/auth | MISSING in Flutter | → N-03..N-08 (NEW) |
| (none) Roles/crew/users mgmt | MISSING | → N-29..N-36 (NEW) |

## 2. Missing screens (features without a defined screen today)
| Screen | Needed for | Why missing now | Priority |
|---|---|---|---|
| Day/Session detail (N-22) | owner day breakdown | Flutter `/details` orphaned | HIGH |
| Attendance overview (N-26) | roll-up across trips | only per-trip in Flutter | MED |
| Reports detail/export (N-28) | export KPI detail | analytics table only | MED |
| Announcements manager (N-35) | owner→all messaging | none | LOW (D-8/phase) |
| Audit log viewer (N-36) | privileged-action review | none (no audit today) | MED (with audit) |
| Role/user mgmt (N-33/34) | admin | none (no auth) | HIGH |
| Notification centre (N-41) | read/unread history | none | MED (with FCM) |
| Account & security settings (N-39) | password/email/2FA? | none | MED |
| Driver screens N-50..53, Labourer N-60..63 | self-service | none (roles D-1) | GATED D-1 |
| Profile (N-40) | own profile | none (single user) | HIGH |
| Password reset screens (N-04/05) | recovery | none | HIGH (auth) |
| Provisioning/invite (N-07) | new-user setup | none | HIGH (auth) |
| Help/About (N-42) | support, privacy, version | minimal | LOW (decision) |

## 3. Orphaned / dead in reference (VERIFIED)
- `/details` screen — orphan route; inert search + filter; misleading swipe delete (BUG-02/03). Native: do not port; replace with N-22.
- `AddQuickTripEvent` empty handler; `ValidationFailure` unused; duplicate switch branches (BUG-05/12/18). Not screens, but dead code to avoid.

## 4. Role-specific screens needing owner confirmation
Whether N-50..N-63 (driver/labourer self-service) exist at all depends on D-1. Without self-service, drivers/labourers are catalogue entries only and those screens are NOT required.

## 5. Missing "recovery/explanation" screens
| Gap | Where | Impact |
|---|---|---|
| Permission-explanation UI | before requesting POST_NOTIFICATIONS/location/photo | trust (A-11) |
| Forbidden explanation with path | when role tries action | clarity |
| Offline banner explaining queued/unsynced | all owner writes | data confidence |
| "Where is my data / backup status" | settings | trust |

## 6. Decision & verification
All gaps recorded. Driver/labourer rows gated D-1. Help/About gated on product. Screen completeness scores in PRODUCT-GAP-REGISTER and scorecards.
