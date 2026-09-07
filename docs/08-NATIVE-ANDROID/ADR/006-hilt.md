# ADR-006: Hilt (Dependency Injection)

## Context
Flutter used get_it. Native needs DI that is compile-safe, integrates with ViewModel/WorkManager/Instrumentation and supports test swapping.

## Decision
Use **Hilt** (Dagger) for dependency injection.

## Why chosen
- Compile-time, AndroidX-integrated (ViewModel, Navigation, WorkManager).
- Test modules can substitute fakes/emulator datasources.
- Aligns with Google/Firebase guidance.

## Alternatives
- Manual DI: boilerplate, error-prone.
- Koin: simpler but runtime, weaker compile safety.

## Trade-offs / consequences
- Adds kapt/ksp processing time; requires Hilt testing setup.
- Keep graph tidy; avoid over-injection.
- ADR-004 layers wired by Hilt.
