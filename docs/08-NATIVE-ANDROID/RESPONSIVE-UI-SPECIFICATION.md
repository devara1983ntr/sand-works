# Responsive UI Specification

Status: Phase 0.5 (PROPOSED). Uses Material3 adaptive (NavigationSuite) per window size; no code. Extends ADR-013 and ANDROID-DESIGN-SYSTEM.

## Window-size classes (Material adaptive)
| Class | Typical | Navigation pattern | Layout behaviour |
|---|---|---|---|
| Compact width | phones portrait | NavigationBar (bottom) | single pane; bottom FAB; content scroll |
| Medium width | small tablets/fold-outer/portrait | NavigationRail | single pane w/ roomier grid; two-pane where sensible |
| Expanded width | tablets/landscape/fold-inner | NavigationRail or suite; two-pane | two-pane (list+detail) for Work, Reports, Crew |
| Foldables | hinge aware | as width class; avoid content in hinge crease | multi/continue; activity re-create |
| Landscape | phones landscape | NavigationBar (or rail if height constrained) | two-pane where useful; scroll; avoid clipping |

## Pattern decisions
- Navigation: use adaptive NavigationSuite in Compose — bottom NavigationBar on compact, NavigationRail on medium/expanded; do NOT force phone bottom-nav onto tablets.
- Two-pane: Work list → editor (select trip → detail in second pane); Reports (period list → detail); Crew (roster list → editor). Reuse for driver/labourer when role screens exist (D-1).
- Content max-width for readability on expanded; lists become multi-column/grid where data supports (crew/report rows).
- FAB placement adapts; dialogs/bottom-sheets constrained width on large screens (centered).
- Landscape/tall-then-wide: support rotation with state preservation (SavedState); no data loss on config change (view model).
- Window resize (desktop/multi-window/split-screen) re-applies breakpoint without losing scroll/data.

## Per-screen responsive notes
| Screen | Compact | Expanded |
|---|---|---|
| Work dashboard | card list + FAB | two-pane list/editor; grid tiles |
| Trip editor | single scroll form | form split into panes or wider column; labour roster side-by-side |
| Attendance | single column toggles | grouped grid with sticky session header |
| Reports | list + detail push | list+detail two-pane; charts side content |
| Crew/user | list | two-pane roster+editor |
| Settings/profile | single column | two-column for grouped settings |
| Notifications/help | single | comfortable max-width |

## Accessibility/behavioral
- Text scaling & landscape never clip (see ACCESSIBILITY-COVERAGE).
- Split-screen / multi-window keep both panes independently functional.
- Test matrix: compact/medium/expanded × portrait/landscape × (foldable hinge) × font scaling, in ANDROID-TESTING-ARCHITECTURE.

## Gaps flagged
- Reference was phone-first; native must be adaptive by default (M3 adaptive). Verify all gated role screens (D-1) adopt same patterns.
- Verification PROPOSED; screenshot/layout tests per breakpoint.
