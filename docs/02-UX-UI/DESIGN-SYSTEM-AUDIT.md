# Design System Audit (current)

Purpose: forensic review of the current visual/UI system vs a professional standard. Status: VERIFIED observations; recommendations PROPOSED. Commit `2dd2fe4`.

## Strengths (VERIFIED)
- Single cohesive dark Material 3 theme applied app-wide via `ThemeData`.
- Strong primary/secondary/success/error semantic palette with consistent usage.
- Distinct content hierarchy on Dashboard (summary → counter → recent trips).
- Reusable components (GlassCard, CustomTextField, PremiumButton, EmptyState, Skeleton) reduce drift.
- Attractive, modern dark "premium" glass aesthetic; brand icon + Poppins/Inter.

## Inconsistencies / weaknesses (VERIFIED)
| ID | Issue | Evidence | Impact |
|---|---|---|---|
| DS-1 | `google_fonts` fetches fonts over network at runtime | `app_theme.dart` uses `GoogleFonts.poppinsTextTheme`/`inter` | In offline-only app fonts may not load → inconsistent typography; contradicts offline-first |
| DS-2 | Mixed glass + gradient button styles | `GlassCard` (translucent blur) with `PremiumButton` (solid gradient+glow) on top | Visual inconsistency, glass blur perf cost |
| DS-3 | No tokenized spacing/type/shape scale; hard-coded paddings/sizes across files | many `EdgeInsets` literals | Hard to retheme/keep consistent |
| DS-4 | Dark-only; no light theme | `themeMode: ThemeMode.dark`; only `darkTheme` set | No light option |
| DS-5 | Low-alpha text (white38/white54) may undercut AA contrast | numerous `Colors.white54`/`white38` | Accessibility risk (see accessibility audit) |
| DS-6 | ScreenUtil fixed 360×690 design width | `main.dart` | Limited adaptive/tablet handling |
| DS-7 | Inconsistent destructive affordance (blue edit icon behind delete swipe) on Details | `details_screen.dart` | Confusing UX |
| DS-8 | Empty-state widget always draws a button even when caller passes empty CTA text | `empty_state.dart` + History/Analytics empty use | Blank buttons in empty states |
| DS-9 | `CardThemeData`/deprecated `Switch.activeColor` usages; a deprecated API ignore present | `trip_details_screen.dart` (ignore: deprecated_member_use) | API hygiene |

## Recommendations (PROPOSED)
1. Bundle fonts (assets) instead of `google_fonts` runtime fetch.
2. Adopt a formal design-token layer (colors/spacing/type/shape/elevation) shared by both current & native.
3. Standardise on either M3 tonal surfaces or glass, not a random mix; evaluate glass blur perf.
4. Add light theme + system/dynamic option.
5. Provide accessibility-safe text colours (WCAG AA) — audit pass needed.
6. Define adaptive/tablet layouts.
7. Fix destructive swipe affordance and empty-state CTA.

## Verification status
VERIFIED: tokens, components, inconsistencies above. Contrast AA numeric checks not run in sandbox (`UNVERIFIED` numeric) — see ACCESSIBILITY-AUDIT.
