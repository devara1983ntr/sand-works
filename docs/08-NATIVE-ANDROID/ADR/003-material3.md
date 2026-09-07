# ADR-003: Material 3

## Context
Need a coherent, accessible, adaptive design system as the base for a premium product UI.

## Decision
Use **Material 3** (stable **1.4.0**) as the foundation, extended with a small custom product design system rather than replacing it.

## Why chosen
- M3 is the modern standard; supports theming, dynamic color, adaptive layouts, large screens, accessibility.
- Build a product layer ON TOP (extend/customize color, type, shape, components) instead of reinventing.
- Do not adopt alpha (1.5.0) merely for a higher version number.

## Alternatives
- Material 2: older, less adaptive/dynamic.
- Fully custom design system: high cost, no accessibility foundations.
- Third-party design systems: unnecessary dependency.

## Trade-offs / consequences
- Follow M3 component + a11y conventions; restrict to stable APIs.
- Dynamic color optional behind a setting (respect brand).
- Coordinate with ANDROID-DESIGN-SYSTEM.md.
