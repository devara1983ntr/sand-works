# Native UX Redesign (proposed)

Status: PROPOSED. Recommends navigation & screen changes grounded in current findings; does not discard validated behaviour. Commit audited `2dd2fe4`.

## 1. What to preserve (VERIFIED behaviour)
Fast data entry (chips for tractor, attendance toggles, copy-next-trip), summary-first dashboard, grouped history, analytics, backup/restore, draft autosave.

## 2. Navigation model (PROPOSED)
- **Owner/Admin:** bottom bar 4–5: Home(Dashboard) · Jobs/Work · Crew · Reports · Settings (top bar bell for notifications). Drill-in detail/edit screens. Orphaned `/details` screen resolved (either removed or becomes owner "day detail").
- **Driver:** My Jobs · Profile (notifications on top bar). Simple, CTA-forward.
- **Labourer (if separate):** My Attendance · Profile.
- Use Navigation Compose; SavedStateHandle to survive process death; deep-linkable job/attendance IDs.

## 3. Redesign drivers & screen changes (each maps to a current finding)
| Finding | Native redesign |
|---|---|
| Orphan `/details` + inert search/filter (BUG-02/03/04) | Remove or make a real "Day breakdown" reachable; implement filter UI via bottom sheet |
| No retry on error | Explicit error states with Retry |
| Misleading delete swipe (BUG-03) | Consistent swipe action: red delete w/ Undo; edit = separate long-press/menu |
| Two overlapping "add trip" flows | Clarify single mental model: "Add Trip" for first of a session vs "Copy last trip" quick-add, clearly labelled |
| Search limited to today | Provide history-wide search/filter on History screen (PROPOSED) |
| Dark-only | Dark+light (+dynamic), tokenized |
| Settings inert tiles | Real theme/about/session, backup & account sections |
| Analytics from full history load | Purpose-built queries/KPIs, paging |
| Empty-state blank CTA (BUG-06) | CTA optional |

## 4. Information architecture (owner) (PROPOSED)
Home summary → Jobs list (day) → Job detail (trips/attendance) → Edit/New trip → confirm. Crew tab → manage drivers/labourers. Reports tab → KPIs & exports. Notifications → role-scoped.

## 5. Gestures (native)
Pull-to-refresh on server lists; swipe-delete with Undo; tap drill-in; bottom-sheet filters; predictive back. Each justified in `02-UX-UI/GESTURES.md`.

## 6. Acceptance
Reuse `SCREEN-SPECIFICATION.md` "Must" list as Compose UI test seeds.

## 7. Status
PROPOSED. All native UX items are recommendations pending product confirmation.
