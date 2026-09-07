# Design System — current (Flutter) vs proposed (native)

Purpose: record the existing visual system (VERIFIED from `lib/theme/app_theme.dart` and widgets) and define the target native system (PROPOSED, Compose + Material 3). Commit `2dd2fe4`.

## 1. Current design tokens (VERIFIED)
Defined in `lib/theme/app_theme.dart`:
| Token | Value | Use |
|---|---|---|
| primary | `0xFF246BFD` (blue) | primary buttons, FAB, accents |
| accent/secondary | `0xFF00D2FF` (cyan) | secondary, icons, highlights |
| success | `0xFF00C853` (green) | present / counts |
| error | `0xFFFF3B30` (red) | delete, errors, absent |
| dark bg | `0xFF0F172A` (slate-900) | scaffold |
| dark surface | `0xFF1E293B` (slate-800) | cards, inputs, dialogs |
| on-colors | white / white70 / white54 / white38 | text hierarchy |
| Theme | `Material3` dark only; `useMaterial3: true` | — |
| Radius | card 16, buttons/fields 12 | shape |
| Elevation | cards 8 (shadow), buttons 4, FAB defaults | depth |
| Fonts | GoogleFonts Poppins (headings) + Inter (body) — **network font fetch at runtime** | typography |

### UI primitives used
`GlassCard` (glassmorphism_ui: blur, translucent white gradient border), `PremiumButton` (linear gradient primary→accent with glow shadow), `EmptyStateWidget`, `CustomTextField`, `SkeletonContainer` (shimmer), Material `NavigationBar`, `ExpansionTile`, `DataTable`, `Switch`, `SwitchListTile`, dialogs.

### Layout
`flutter_screenutil` design size **360×690**, `minTextAdapt`, `splitScreenMode`; padding conventions mostly 16dp page / 24dp spacing.

## 2. Design-system audit summary (VERIFIED)
See `DESIGN-SYSTEM-AUDIT.md`. Headlines:
- Cohesive, premium-feeling dark glass UI; good hierarchy on the main cards.
- Dark-only (no light theme); glassmorphism & gradient in one component mix is inconsistent (PremiumButton gradient inside GlassCard).
- `google_fonts` fetches fonts at runtime → **offline mode may fall back to system fonts** (contradicts offline-first) — recommended to bundle fonts.
- No spacing/type/shape tokens as a formal scale; hard-coded paddings across widgets.
- Contrast: white54/white38 on dark surfaces may be below AA for body text (audit to confirm).

## 3. Proposed native design system (PROPOSED — Jetpack Compose + Material 3)
Target: premium, polished, production-grade consumer app. Stable M3 APIs (M3 Expressive only if product-approved).
- **Color scheme:** Material 3 dynamic `ColorScheme.fromSeed` around a brand blue; semantic success/error/warning; explicit dark & light schemes + optional dynamic color (respect user).
- **Tokens via Compose theme:** `BrandColors`, `Md3ColorScheme`, spacing 4/8/12/16/24/32, shape tokens (small 8, medium 12, large 16/24), elevation/shadow.
- **Typography:** bundled font family (avoid runtime fetch); type scale from M3 (display/title/body/label) with Poppins/Inter-equivalents bundled.
- **Surfaces/cards:** M3 `Card` with tonal surfaces; use tonal/elevated rather than glass for accessibility & performance; optional hero/featured imagery per content type.
- **Buttons/fields:** M3 Filled/Outlined/Tonal buttons; OutlinedTextField; consistent 48dp targets.
- **Components:** chips, badges, lists (LazyColumn), loading (Circular/Linear + skeletons), empty/error states with retry, snackbars, dialogs, top/bottom nav per role.
- **Motion:** Material motion (shared axis/fade) + reduced-motion support.
- **Responsive:** canonical layouts for phone; adaptive/tablet width classes.
- **No adult/sexual content; premium = strong hierarchy, polished media, clear CTA, discovery & personalization** for a legitimate labour-management product.

See `08-NATIVE-ANDROID/ANDROID-DESIGN-SYSTEM.md` for the full native spec.
