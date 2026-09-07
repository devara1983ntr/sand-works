# FINAL RESPONSIVE DESIGN GATE — V1 (Phase 0.75)

Not "scale the phone layout". Define adaptive navigation + content per window class (M3 adaptive). No code. Reference ADR-013 + RESPONSIVE-UI-SPECIFICATION reconciled.

## Window-size behaviour
| Class | Devices | Navigation | Layout |
|---|---|---|---|
| Compact | phones portrait | NavigationBar (bottom): Dashboard·Work·Reports·More | single pane; FAB; scroll; dialog/sheet full-width-ish |
| Medium | small tablets/fold-outer/portrait large phone | NavigationRail | single pane + roomier grid; two-pane where defined |
| Expanded | tablets/landscape/fold-inner/desktop-resize | NavigationRail/NavigationSuite | two-pane: list+detail (Work trips, Reports, Catalogues) |
| Landscape phone | phones landscape | NavigationBar (or Rail if height-limited) | two-pane where useful; no clipping; scroll |
| Foldable | hinge-aware | per width class | no content in hinge crease; activity re-create safe; dual-screen continue |
| Split-screen / multi-window | resizable | breakpoint recompute live | both panes independently functional; SavedState preserved |

## Adaptive rules (mandatory)
1. Use M3 adaptive NavigationSuite: bottom NavigationBar on compact, Rail on medium/expanded. Never force phone bottom-nav onto tablets.
2. Two-pane: trip list↔editor, reports list↔detail, labour/driver list↔editor; reuse for all roles when they exist (D-1).
3. Content max-width on expanded for readability; lists may become grid/multi-column where data supports (crew/report rows).
4. FAB/dialog/bottom-sheet placement adapts; large-screen dialogs centered & width-constrained.
5. Rotation/resize/multi-window: state preserved (SavedState + ViewModel), no data loss on config change; breakpoint re-applied live.
6. Landscape & text-scaling never clip (FINAL-ACCESSIBILITY); no horizontal-only layouts.
7. Font-scale and density don't collapse touch targets (<48dp) — verify at each breakpoint.

## Content adaptation notes
- Dashboard: compact card list; expanded grid/two-pane.
- Attendance N-23: single column toggles compact; grouped roster grid with sticky session header expanded.
- Reports: list+detail two-pane expanded; charts side content.
- Settings/profile: single column compact; two-column grouped settings expanded.

## Tests
Screenshot/layout tests at compact/medium/expanded × portrait/landscape × (foldable) × font scaling × split-screen (FINAL-TEST-CONTRACT). Layout lint asserts no target <48dp and no clip.
