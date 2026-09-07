# ADR-013: Navigation architecture

## Context
Flutter used go_router with `extra` object passing (fragile, BUG-17) and an orphaned route. Native needs predictable, role-driven navigation with correct Back/logout/session behaviour and adaptive large-screen support.

## Decision
Use **Navigation Compose** with:
- AuthGraph (splash/onboarding → login → provisioning) and MainGraph (role-based home + bottom destinations) and DetailGraph (pushed editors/details).
- Stable **IDs** loaded via repository for detail screens (no whole-object `extra` passing).
- Back/logout/session-expiry semantics defined (see NATIVE-NAVIGATION.md).
- Adaptive: NavigationRail + two-pane on medium/large via Material window size classes.

## Why chosen
- Type-safe, Compose-integrated, testable.
- Fixes Flutter `extra` fragility.
- Role-driven graph + adaptive support.

## Alternatives
- go_router: Flutter-era, N/A native.
- Manual navigation: error-prone.
- Navigation 3 experimental: not stable — avoid.

## Trade-offs / consequences
- Two nav graphs (auth/main) with a role gate.
- Deep links re-validated by rules.
- Keep ≤5 bottom destinations (current design 4).
