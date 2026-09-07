# Accessibility Coverage

Status: Phase 0.5 (PROPOSED). Reference had a single accessibility page (F-28) which is not a proxy for system accessibility. Each screen must meet TalkBack, touch-target, contrast, text scaling, keyboard, motion, colour-independence. Extends ANDROID-DESIGN-SYSTEM + senior UX review (§11 of that doc).

## Global a11y contract
- Touch targets ≥ 48dp (48–64 for primary).
- Contrast: AA text (4.5:1 normal, 3:1 large) incl. enabled states; respect dynamic theme (light/dark).
- Text scaling: layouts must not clip at 200% font scale; use dynamic type.
- Semantics/contentDescription on all informative icons & states; live-region announcements for async outcomes (loading→success/error).
- Colour is never the sole indicator (use icon+label+shape too); includes "present/absent" toggles, error borders, connectivity.
- Keyboard: all actions reachable; visible focus ring; dialogs/bottom-sheets have focus trap & esc/dismiss.
- Motion: reduce motion respected; no unskippable animation; swipe actions have button alternative.
- Focus: restore on error; error summary announced.
- Proper role/semantics (toggle, combobox) so TalkBack announces state; grouping of labour list.

## Per-screen checklist
| Screen | TalkBack order | Content | Targets | Contrast | Text scale | Focus | Motion | Colour-indep | Keyboard |
|---|---|---|---|---|---|---|---|---|---|
| Login | fields→CTA→links | labels/hints | ≥48 | AA | no clip | first field | minimal | error text not colour-only | enter submit |
| Dashboard/work list | nav→content→FAB | trip summary read | ≥48 | AA | no clip | focus trap FAB? | none | status icon+label | tab to actions |
| Trip detail attendance | name→toggle→state | toggle state announced | ≥48 | AA (toggle) | roster not clipped | row focus | none | present/absent has icon+label | toggle via space |
| Editors/forms | label→field→error→submit | error announce | ≥48 | AA | labels+field scale | autofocus first required? | none | required marked textually | IME next/done |
| Search/filter | search→results | results count | ≥48 | AA | — | results focus | no flicker | filter state not colour-only | arrow nav results |
| Reports | headers→rows→export | summary | ≥48 | AA | no horizontal only | row | reduce motion | sort arrow+label | arrow nav |
| Crew/user mgmt | list→actions | row roles | ≥48 | AA | no clip | action menu | none | active/inactive icon+label | menu buttons |
| Notification centre | unread first | announce new | ≥48 | AA | — | list | live region on new | unread dot+label | — |
| Profile/settings | fields→save | result announce | ≥48 | AA | no clip | — | — | — | — |
| Confirm dialogs | message→confirm/cancel | destructive announced | ≥48 | AA | — | focus on cancel (safe default) | none | cancel = safe default | esc cancels |

## Errors & offline
- Errors announced (live region) not only a snackbar colour.
- Offline banner announced; "Syncing…" progress announced; never rely on colour for offline.
- Destructive confirm dialog: confirm/cancel have text; focus defaults to cancel (safe).

## Gaps flagged
- Reference single a11y page → native must implement per-screen; ensure TalkBack on attendance toggles/state not ambiguous; keep icon+text dual coding for all states. gated screens D-1/D-6 still carry checklist.
- Verification PROPOSED; a11y checks in UI tests (TalkBack semantics assertions, contrast, touch-target) + manual audit list.
