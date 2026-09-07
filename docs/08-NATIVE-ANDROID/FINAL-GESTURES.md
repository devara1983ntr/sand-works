# FINAL GESTURES — V1 (Phase 0.75)

Every gesture: target, purpose, feedback, success, cancel, a11y alternative, destructive protection. No gesture overload; no novelty gestures; every gesture has a non-gesture (a11y) alternative. Reference gesture audit reconciled.

| Gesture | Screen | Target | Purpose | Feedback | Success | Cancel | A11y alt | Destructive protection |
|---|---|---|---|---|---|---|---|---|
| Tap | all | rows, buttons, nav, FAB | primary action | ripple | run action | — | TalkBack activate / keyboard Enter | destructive → confirm |
| Tap toggle | N-23 | presence toggle | set present/absent | state change | update | revert | toggle semantics/space | none (non-destructive; see reason-on-change) |
| Tap "+" | N-20/N-25/N-29 | FAB/button | create | — | navigate to editor | — | button | — |
| Long-press | N-23/N-29/N-31 list rows | row | reveal context actions (edit/correct) | context menu/ripple | open | dismiss | visible icon menu | delete/remove still confirm |
| Swipe-to-delete | N-20/N-23/N-29 | row | delete/remove | red reveal | confirm → soft-delete+undo | snap-back | menu → Delete | confirm + undo (soft) |
| Pull-to-refresh | lists (N-20/27/29…) | list | refresh | top spinner | refetch | revert | action button in top bar | n/a |
| Horizontal paging | N-27 (period) optional | content | change period/day | page transition | switch | revert | prev/next buttons | n/a |
| Bottom-sheet drag | pickers/filter | sheet | adjust/apply | sheet tracks finger | apply | snap-close | buttons | destructive options confirmed |
| System back / predictive | all | Android | go back | predictive preview | pop | abort | top-left back | unsaved → autosave/discard-confirm |
| Scroll / nested scroll | lists, forms, editors | content | navigate content | native | — | — | TalkBack scroll | n/a |
| Keyboard | forms, search | field | input | caret/focus | IME action | — | hardware/keyboard nav | — |

## Per-screen primary set (to avoid overload)
- N-20: tap row/trip, tap + (FAB), pull-refresh, swipe-to-delete, tap search/filter.
- N-23: tap toggles, tap add-labour, menu (long-press optional) for correct/edit/remove.
- N-24/25: tap save/cancel/back, chips, pickers, keyboard.
- N-27: tap sort header, tap row→detail, export, (period paging optional).
- N-29/30/31: tap row→detail, add, remove (menu/confirm), swipe optional but with a11y alt.
- Backup/restore N-38: tap actions only (no swipe).

## Rules
- Reveal colour/icon matches the actual action (fixes reference misleading swipe).
- Destructive actions always confirm + undo where reversible.
- Swipe only end-to-start so it doesn't fight vertical scroll; long-press/menu remain equivalent.
- Every gesture maps to a Compose UI test + a TalkBack/keyboard alternative test (FINAL-TEST-CONTRACT / FINAL-ACCESSIBILITY).

## No-novelly guard
Drag/reorder, force-touch, shake, multi-select gestures NOT added without a product requirement.
