# FINAL ACCESSIBILITY GATE — V1 (Phase 0.75)

For every V1 screen, the a11y axes below are marked PASS/FAIL/NOT-APPLICABLE (gate = PASS or NA before ship; reference a11y coverage reconciled). Global design-system a11y in ANDROID-DESIGN-SYSTEM; per-screen detail in ACCESSIBILITY-COVERAGE.

Axes: TalkBack order & semantics · Touch target ≥48dp · Content description · Focus order · Text scaling (no clip @200%) · Contrast AA · Error announcements (live region) · Keyboard reachability · Reduced motion · Colour-independence.

## Common contract (applies to all screens → marked PASS at design)
- Informative icons get contentDescription; decorative hidden; groups (attendance roster) use combined semantics; toggles announce state.
- Touch targets ≥48dp (primary 48–64). Focus order follows visual/reading order; visible focus ring.
- Dynamic type no clipping; key text/labels contrast AA (light+dark); never colour-only (status has icon+label; connectivity has banner text; errors have text).
- Async/state changes announced via live region (loading→success/error/sync).
- Keyboard: all actions reachable; dialog/bottom-sheet focus-trap + esc dismiss.
- Reduced motion respected; swipe has button alternative (FINAL-GESTURES).

## Per-screen gate table (P=Pass/design, NA=not applicable)
| Screen | TalkBack | Target | ContentDesc | Focus | Scale | Contrast | ErrorAnnounce | Keyboard | Motion | Colour-indep |
|---|---|---|---|---|---|---|---|---|---|---|
| N-01 Splash | P | P | P | P | P | P | P | P | P | P |
| N-03 Login | P | P | P | P | P | P | P(login err) | P | P | P |
| N-04/05 Reset | P | P | P | P | P | P | P | P | P | P |
| N-06/07 Disabled/Provision | P | P | P | P | P | P | P | P | P | P |
| N-08 Session | P | P | P | P | P | P | P | P | P | P |
| N-10 Shell | P | P(nav≥48) | P | P | P | P | P | P | P | P(active tab icon+label) |
| N-20 Dashboard | P | P | P | P | P | P | P | P | P | P |
| N-22 Day | P | P | P | P | P | P | P | P | P | P |
| N-23 Trip+attendance | P | P(toggles) | P | P | P(roster no clip) | P(toggle state) | P | P | P | P(present/absent icon+label) |
| N-24/25 Editors | P | P | P | P(focus required) | P | P | P(field+async) | P | P | P(required marked textually) |
| N-27 Reports | P | P | P | P | P | P | P | P(arrow nav) | P(reduce motion) | P(sort icon+label) |
| N-29/30/31 Catalogues | P | P | P | P | P | P | P | P(menu btns) | P | P(active icon+label) |
| N-36 Audit | P | P | P | P | P | P | P | P | P | P |
| N-37 Settings | P | P | P | P | P | P | P | P | P | P |
| N-38 Backup/restore | P | P | P | P | P | P | P(progress live) | P | P | P |
| N-39 Account/security | P | P | P | P | P | P | P | P | P | P |
| N-40 Profile | P | P | P | P | P | P | P | P | P | P |
| N-41 Notifications(S4) | P | P | P | P | P | P | P(new unread) | P | P | P(unread dot+label) |
| N-42 Help | P | P | P | P | P | P | P | P | P | P |

## Gate rule
A screen ships only when all applicable axes PASS; exceptions need a documented, owner-approved reason. Verification: automated (Compose semantics assertions, contrast lint, touch-target) + manual TalkBack pass list → FT-A11Y + release gate (PRE-RELEASE.md).
