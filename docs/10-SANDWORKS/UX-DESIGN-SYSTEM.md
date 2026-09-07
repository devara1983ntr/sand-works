# UX DESIGN SYSTEM — SAND WORKS

Personality: POWERFUL · MODERN · INDUSTRIAL · TRUSTWORTHY · PREMIUM · CLEAN · PRACTICAL. The logo is dramatic; the UI stays disciplined. Do NOT make every screen look like the logo. Premium industrial operations, not generic CRUD.

## Colour system
Brand:
- Primary Orange `#F97316`
- Sand Gold `#F5B942`
- Deep Charcoal `#0B0D0F`
- Graphite `#15191D`
- Slate `#20262B`
- Steel `#66717A`
- Off White `#F5F7F8`
- Muted Text `#A7B0B7`

Light theme:
- Background `#F6F7F8`
- Surface `#FFFFFF`
- Primary `#EA580C`
- Text `#111518`
- Secondary Text `#5F686E`
- Border `#DCE1E4`

Orange is an **accent**, not the entire interface. Provide light/dark dynamic theme tokens (dark = charcoal/graphite/slate surfaces). Contrast AA; never colour-only meaning.

## Typography
- Primary UI font: **Roboto** — Regular, Medium, SemiBold, Bold.
- Strong/display treatment only for branding. Decorative industrial fonts never used for body text. Accessibility + readability first.
- Localisation-ready string resources; dynamic type no clip.

## Material 3
- Kotlin + Jetpack Compose + Material 3.
- Use M3 components consistently. Custom primitives only where M3 lacks the required interaction. Do not over-customise.

## Shape
- Preferred corner radius **12–16dp**.
- Avoid excessive pill shapes, giant rounded cards, excessive glassmorphism, decorative 3D UI.

## Navigation
- Mobile: **Home · Work · Trips · More** (Owner); role-specific tab sets for Driver/Labourer. Material icons; **orange = active state**.
- Large screens: Navigation Rail / adaptive navigation + two-pane where useful.

## Dashboards
- Owner: today's trips/work/money; active drivers/labourers; recent activity; alerts; large scannable metrics; quick access; avoid clutter.
- Driver: today's trips, selected tractor, assigned labourers, today's summary, recent trips, **+ ADD TRIP** primary.
- Labourer: read-only totals/earned/remaining/working/absent/dates + weekly/monthly rank.

## States
Loading(skeleton) · Success · Empty(truthful+actionable) · Error(typed+retry) · Offline(cached+indicator) · Submitting(disabled) · Conflict(explicit) · Forbidden · Session-expired. Every visible element intentional and functional; no placeholder UI (AGENT §14).

## Brand surfaces
Use full logo: splash, About, selected brand surfaces, docs, onboarding. Use icon: launcher, compact brand identity, auth header, notification identity. Use masters only; no stretching/distortion/recreation.
