# SCREEN CATALOG — SAND WORKS V1

Role-scoped screen set. Navigation structure (per role) with the mobile pattern Home/Work/Trips/More (Owner) and role-specific tabs, plus large-screen NavigationRail + two-pane. Orange = active state.

## Shared design
Bottom nav: **Home · Work · Trips · More** (Owner). Driver: **Home · Trips · More**. Labourer: **Home · More**. Orange active state. M3 adaptive (Rail/two-pane on large screens). App-bar back per Android; every screen has defined back; no orphan/dead-end.

## OWNER screens
| Screen | Purpose | Key content/actions |
|---|---|---|
| O-Home (Owner Dashboard) | operational overview | today's trips/work/money; active drivers/labourers; recent activity; alerts; large scannable metrics; quick access Users/Drivers/Labourers/Tractors/Trips/Rates/Attendance/Leaderboard/Reports/Export/Settings/Alerts |
| O-Approvals | approve/reject driver & labourer registration | list pending; approve/reject (CF, audit) |
| O-Users | manage users | list; status/role; suspend/activate; audit |
| O-Drivers | manage driver records | CRUD; approve; stats |
| O-Labourers | manage labourers | CRUD; attendance; stats |
| O-Tractors | tractor registry | add/edit/deactivate (Sonalika, John Deere initial) |
| O-Trips / Trip editor | view/all + create/edit trips (full authority) | date/time/tractor/driver/labourers/rate snapshot/total/status |
| O-Attendance / corrections | work-day tracking | working/absent; correction w/ reason+audit |
| O-Rates | configure per-trip rate | default ₹200; change (future snapshot) |
| O-Money rules | configure distribution | equal (default)/driver share/labour share/custom %/fixed; preview |
| O-Leaderboard | weekly+monthly | top-3 real, weekly/monthly toggle |
| O-Reports / Export | owner-only | PDF + CSV; multi-tractor/driver/labour/date |
| O-Alerts | send operational warning | message optional; recipients; high-priority alert UI |
| O-Assignments | temp labour assignment | labourer/assignor/start/end/reason/status; expiry |
| O-Settings | config | rates, tractors, money rules, summary time, notifications, profiles, backup |
| O-Audit (view) | audit trail (owner) | read-only server-authoritative log |
| Profile / About | profile + brand | SAND WORKS locked assets; version |

## DRIVER screens
| Screen | Purpose | Key content/actions |
|---|---|---|
| D-Home (Driver Dashboard) | today | today's trips; selected tractor; assigned labourers; today's summary; recent trips; **+ ADD TRIP** (primary) |
| D-Trips / Add trip | create/edit own permitted trips | date/time/tractor/labourers; own scope |
| D-Assigned/Operational | assigned operational info | view labour info required for work |
| D-My Totals | own work/trip totals | today's total; share via WhatsApp/share-sheet (real count) |
| Profile | own profile | name/role/picture/status/stats |
| Notifications | targeted | assignment/approval/alerts/events (own) |

## LABOURER screens
| Screen | Purpose | Key content/actions |
|---|---|---|
| L-Home (Labourer Dashboard, read-only) | personal stats | TOTAL TRIPS, TOTAL EARNED, REMAINING MONEY, WORKING DAYS, ABSENT DAYS, WORKING DATES, ABSENT DATES; weekly+monthly rank; weekly trip total |
| L-Working trips / history | date-based work history | own working/absent dates |
| L-Weekly / Monthly leaderboard | ranking view | top-3 own position (real) |
| Profile | own profile | picture, stats, status |
| Notifications | targeted | daily earning summary; alerts; approval result; assignment/expiry |

## States (applies to every screen, per UX/quality specs)
Every screen defines: Loading (skeleton), Success, Empty (truthful, actionable), Error (typed + retry), Offline (cached + banner/queue indicator), Submitting (spinner, disabled), Conflict (explicit resolution), Forbidden (role check), Session-expired (re-auth preserving state). No fake success/loading/empty.

## Role gate / back / entry
Each screen lists its owning role, entry points, parent→child, and back target in the implementation control. No orphan screen. Labourer screens are read-only; owner screens never reachable by driver/labourer (route + rules both enforce).
