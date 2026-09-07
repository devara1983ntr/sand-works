# Native Android Design System (specification)

Status: PROPOSED design spec (Phase 0) — Jetpack Compose + Material 3 **stable (1.4.0)**; do not adopt 1.5.0-alpha. Visual target: premium, modern, polished, consumer-grade (no adult content; premium refers to hierarchy, media, cards, CTAs, motion, discovery). This is a custom product design system **on top of** Material 3 (extend/customize), not a replacement.

## 1. Brand & identity
- Brand: "Labour Party" (resolve identity/package with owner — OQ-2).
- Logo: current `app_icon_192.png` as source; create adaptive launcher icon + in-app mark.
- Iconography: Material Symbols/Outlined consistent set; brand-only accents.

## 2. Color system
- Build from brand seed (current primary `#246BFD`-like blue → cyan accent `#00D2FF`) via `dynamicLightColorScheme/dynamicDarkColorScheme` or fixed `MaterialTheme`.
- Semantic: success (present/attendance), error, warning, info mapped to M3 containers.
- Dark + light schemes; optional dynamic color (from wallpaper) behind a setting.
- Provide contrast-AA variants; never rely on alpha-38/54 for body text.

## 3. Typography
- Bundle fonts (Poppins for display/titles + Inter or similar for body) — no runtime fetch.
- M3 type scale (display/headline/title/body/label) mapped; line-height & tracking sane; supports large font scale.

## 4. Shape / spacing / elevation
- Shape scale: small 8 / medium 12 / large 16 / extra-large 24 (cards 16/24).
- Spacing: 4/8/12/16/24/32 system; page gutters 16.
- Elevation via M3 tonal + shadow tokens; elevation is semantic not decorative.

## 5. Core components
- Surfaces/cards (Card, tonal/elevated/outlined), Buttons (Filled/Outlined/Tonal/Text), TextFields (Outlined, filled), Dialogs & Sheets (bottom sheets for filters), TopBar, BottomBar + NavigationRail (tablet), Chips, Badges, Lists (LazyColumn w/ keys), Loading (Linear/Circular + skeletons), Empty/Error states with retry, Snackbars, Swipe/Undo actions.

## 6. Motion & transitions
- Material motion tokens; shared-axis for screens; content fade/scale; keep subtle; honor `reduced motion`.

## 7. Responsive / large screen
- Phone canonical; width-based adaptive: use `BoxWithConstraints`/`MaterialWindowSizeClass`; NavigationRail + master/detail on tablets; multi-pane owner dashboard where valuable.

## 8. Accessibility
- Semantics everywhere, 48dp targets, AA contrast, large-font safe, reduced motion, TalkBack announcements (see ACCESSIBILITY.md).

## 9. Theming file layout (Compose)
`designsystem/theme/{Color,Type,Shape,Elevation}.kt`, `Theme.kt` (light/dark/dynamic), reusable component composables in `designsystem/components`.

## 10. Do-not
- No sexual/adult content; no imitation of such sites; keep a legitimate labour-management aesthetic: strong hierarchy, polished cards, clear CTAs, smooth transitions, good discovery, personalization within role.

## 11. Senior UX design review (Phase-0 §40)
| Question | Answer/commitment |
|---|---|
| Is the primary action obvious? | Each screen has one primary CTA (e.g., dashboard "Record trip"); hierarchy reinforces it |
| Can users understand a screen within seconds? | Summary-first layouts; familiar M3 patterns |
| Is navigation predictable? | Role-driven bottom bar (≤5), consistent Back, master–detail on large screens |
| Is back behaviour consistent? | Yes — detail returns to origin; predictive-back; defined for logout/session-expiry |
| Are destructive actions protected? | Confirm + (owner-only) + audit + soft-delete + undo where reversible |
| Are loading states informative? | Skeleton/spinner + progress; offline indicator distinct |
| Are empty states useful? | Helpful empty states with a next-step CTA (fix Flutter blank-CTA bug) |
| Are errors recoverable? | Typed states with Retry (fix no-retry gap) |
| Is role-specific UI clear? | Role drives available actions; forbidden paths show clear Forbidden state, never fake success |
| Is information density appropriate? | Owner dashboards rich; labourer/driver minimal; adjustable reports |
| Does UI scale to larger devices? | Adaptive rails/two-pane; not phone layout stretched |
| Is accessibility preserved? | Semantics, 48dp targets, AA contrast, large-font, reduced-motion |
| Are animations purposeful? | Yes — only aid comprehension/feedback; honor reduced-motion |
