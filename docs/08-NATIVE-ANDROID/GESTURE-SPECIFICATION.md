# Gesture Specification

Status: Phase 0.5 (PROPOSED). Reference gesture audit (GESTURES.md) carried forward; each gesture: target, purpose, feedback, threshold, success/cancel, a11y alternative, conflicts, destructive protection. Only add gestures with a usability/role justification.

## Gesture register (native)
| Gesture | Target | Purpose | Visual feedback | Success | Cancel | A11y alt | Destructive protection |
|---|---|---|---|---|---|---|---|
| Tap | rows, buttons, nav | primary action | ripple | run action | — | double-tap/click via TalkBack | destructive = confirm |
| Double-tap | (none default) | n/a | — | — | — | not needed | — |
| Long-press | list rows | reveal context actions (edit/assign) | context menu/ripple | open menu | dismiss | explicit icon/menu buttons | delete still confirm |
| Swipe-to-delete | trip/attendance rows | delete/remove | red reveal (delete) | confirm → soft-delete + undo | revert snap | menu → Delete | confirm dialog + undo |
| Pull-to-refresh | list screens | refresh | top spinner | refetch | revert | action button in top bar | n/a |
| Drag/reorder | (not needed) | n/a | — | — | — | — | not introduced |
| Horizontal paging | reports/dates (optional) | change day/period | page transition | switch | revert | prev/next buttons | n/a |
| Drawer swipe | if drawer used | open nav | edge reveal | open | close | bottom nav/menu | n/a |
| Bottom-sheet drag | filter/announcement sheets | adjust/apply | sheet tracks finger | apply/cancel | snap-close | buttons | destructive options confirmed |
| System back/gesture | Android | go back | predictive back | pop | revert | top-bar back | unsaved confirm/draft |
| Scroll / nested scroll | lists, forms | navigate content | native | — | — | scroll via TalkBack | n/a |
| Keyboard | forms, search | input | focus/caret | Enter action (IME) | — | keyboard nav | — |

## Per-screen gesture mapping (owner)
| Screen | Primary gestures | Notes |
|---|---|---|
| N-20 Dashboard | tap trip, tap +, swipe delete, pull-refresh | |
| N-23 Trip detail | tap toggle, swipe? (attendance row remove) | remove=icon+undo (not swipe) |
| N-24/25 | keyboard, tap save, back | autosave |
| N-27 Reports | tap sort header, tap detail, horizontal period paging | |
| N-29..32/33 | tap row, swipe reveal edit/active toggle | active toggle destructive → confirm |

## Rules
- Match reveal colour/icon to the actual mapped action (fixes reference BUG-03 misleading swipe).
- Every gesture has an accessibility alternative (visible action).
- No gesture for novelty; destructive actions always have confirm and undo where reversible.
- Swipe vs scroll conflict: only swipe horizontally (end-to-start) so it does not fight vertical scroll.

## Gaps flagged
- Reference: `/details` had a misleading delete-affordance gesture → not carried.
- No drag/reorder justified → not introduced.
- Verification PROPOSED; Compose UI gesture tests + a11y alt per gesture.
