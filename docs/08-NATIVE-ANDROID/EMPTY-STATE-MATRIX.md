# Empty State Matrix

Status: Phase 0.5 (PROPOSED). Every list/query defines an empty state: icon, explanation, primary action, secondary action, role-specific. No meaningless blank screens.

| Empty scenario | Screen | Icon | Explanation | Primary action | Secondary | Role note |
|---|---|---|---|---|---|---|
| No work today | N-20 | work-outline | "No work recorded for today" | Record first trip (N-25) | View history/reports | owner only |
| No trips in a session | N-22/21 | inbox | "No trips in this session" | Add trip | back | owner |
| No labour assigned to a trip | N-23 | person-off | "Add labour to record attendance" | Add labour (picker) | back | owner; labourer sees own absence info |
| No labourers/drivers in org | N-31/29 | group-off | "Add your crew to start" | Add labourer/driver | — | owner; if empty, attendance can't fully record |
| No users | N-33 | person-add | "No user accounts yet" | Invite user | — | owner; cannot run roles empty |
| No notifications | N-41 | notifications-none | "No notifications" | — | settings | all; still show centre |
| No search results | N-20/27 | search-off | "No matches for '…'" | Clear search | refine filters | all roles |
| No history/reports | N-27 | bar-chart-empty | "No data to report yet" | Record work | — | owner; driver own-only empty |
| No assigned work (driver, D-1) | N-50 | clipboard | "No trips assigned" | (pull to refresh) | contact owner | driver |
| No attendance (labourer, D-1) | N-60 | calendar-blank | "No attendance on record" | (refresh) | profile | labourer |
| Offline + empty (no cache) | many | wifi-off | "Offline — no cached data" | Retry when online | — | all |
| No audit events | N-36 | shield | "No privileged actions yet" | — | — | owner/admin |
| No backups | N-38 | backup-none | "No backups yet" | Create backup | enable cloud | owner |

## Rules
- Every empty state provides a next step (action) where one exists and is meaningful.
- Empty ≠ error: empty is expected (no data); error is failure (see ERROR matrix).
- Fix reference bug: no blank CTA buttons in empty states (audit BUG-06).
- Role-appropriate: driver/labourer never see "create" CTAs they can't do.
- Empty state also used when a query returns zero due to filters → offer "clear filters".

## Gaps flagged
- Reference History/Analytics empty states had blank CTA (BUG-06) → fixed by optional CTA.
- Offline+empty-cache needs its own messaging (not misleading "no data").
- Verification PROPOSED; empty-state per role tested.
