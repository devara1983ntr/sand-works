# ADR-004: Clean Architecture

## Context
The Flutter reference used a "clean-ish" layered approach but with presentation→data leaks, dead code and business logic in screens. Native needs maintainable, testable boundaries and a backend-independent domain.

## Decision
Use **Clean Architecture** layering: Presentation → Domain ← Data, with domain independent of Firebase/Android. Repository interfaces are the seam. Use cases hold business rules.

## Why chosen
- Domain testability & independence from Firebase/Room.
- Repository interface enables local/remote/emulator swapping.
- Keeps UI free of backend/business logic.
- Matches audit recommendation; fixes the Flutter smell (presentation→Hive leaks).

## Alternatives
- Single-layer "everything in Activity/ViewModel": faster initially, but untestable, unmaintainable, high tech-debt (the very issues flagged).
- Unbounded modularization: over-engineered for this size (avoid).

## Trade-offs / consequences
- More files/boilerplate; discipline required (no shortcuts into data from UI).
- Repository + mapper overhead.
- ADR-006 (Hilt) wires the layers.
