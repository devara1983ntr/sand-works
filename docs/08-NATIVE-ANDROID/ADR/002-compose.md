# ADR-002: Jetpack Compose

## Context
Need a modern declarative UI toolkit that scales from phone to tablet, integrates with ViewModel/Flow, and supports a strong design system.

## Decision
Use **Jetpack Compose** for all UI.

## Why chosen
- Declarative UDF fits ViewModel/StateFlow architecture.
- Material 3 + adaptive layouts support large screens.
- Official stable (Compose core stable; use Compose BOM mapping stable core, verify version).
- Testability (Compose UI tests).

## Alternatives
- View/XML: more verbose, imperative, harder to adapt & test.
- Third-party UI kits: not needed, avoid dependency.

## Trade-offs / consequences
- Compose learning curve; recomposition discipline required (stable state, keys).
- Some 1.12+ features require compileSdk 37/AGP 9 — keep to stable supported set.
- ADR-003 (Material 3) layered on Compose.
