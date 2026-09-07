# Gestures Audit

Purpose: document every implemented gesture and propose native gestures (PROPOSED), each with a usability justification. Status: VERIFIED unless PROPOSED. Commit `2dd2fe4`.

## Implemented gestures (VERIFIED)
| Gesture | Location | Trigger/behaviour | Justification |
|---|---|---|---|
| Tap | Dashboard trip row | push `/trip-details` | standard drill-in |
| Tap | History trip row / icons | details / edit / delete | standard |
| Tap | Analytics column header | sort asc/desc | data table sorting |
| Tap | Dashboard current-trip **+** | add next trip | quick add |
| Tap | Dashboard current-trip **−** | remove latest (confirm) | undo-last |
| Long-press | none | — | not used |
| Swipe left (end-to-start) | Dashboard trip list (`Dismissible`) | delete (confirm) | quick delete |
| Swipe horizontal | Details trip list | `endToStart`→delete, else→edit | edit/delete (but see finding: misleading affordance, both backgrounds show edit icon while delete triggers red dialog) |
| Pull-to-refresh | Dashboard | reload current session | freshness |
| Scrolling | All lists/forms | standard | content paging |
| Drag | none | — | not used |
| Pinch/zoom | none | — | N/A |
| Dismiss/bottom-sheet/drawer gestures | none | no sheets/drawers | N/A |
| Keyboard | Search, forms | typing, Enter | standard |
| Back edge/system | Android | pop / exit | platform |

## Findings (VERIFIED)
- G-1: Details screen `Dismissible` reveals a **blue edit icon** for a gesture that maps to **delete**; misleading. Recommended fix in native: background colour/icon must match the mapped action (delete=red trash, edit=blue/pencil) and swipe direction semantics consistent with dashboard.
- G-2: No undo for trip delete (only labour remove has Undo). Consider Undo for trip delete (PROPOSED).
- G-3: Dashboard list is a `ListView.builder` inside a scroll view with `NeverScrollableScrollPhysics` — vertical scrolling of the whole page, not the inner list; acceptable but note nested-scroll behaviour.

## Proposed native gestures (PROPOSED)
| Gesture | Where | Justification |
|---|---|---|
| Tap with proper ripple + haptics | buttons/rows | tactile feedback |
| Pull-to-refresh on lists backed by server | owner/driver job lists | data freshness |
| Swipe actions w/ role-gated delete/archive & Undo | job/attendance rows | fast triage w/ recovery |
| Bottom-sheet for filter/role menus | dashboard filters | contextual, avoids nav clutter |
| Standard LazyColumn scroll + scroll-to-top | long lists | perf & UX |
| Back/swipe gesture | Android predictive back | platform-native |

## Rule
Only add gestures with clear usability + role-permission justification. Do not add cosmetic-only gestures.
