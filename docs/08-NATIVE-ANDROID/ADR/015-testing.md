# ADR-015: Testing strategy

## Context
Reference had a broad but un-executed-in-this-env Flutter test suite with gaps. Native must make security-critical Firebase rules, role behaviour, offline/sync, and every screen's states automatically tested.

## Decision
Adopt the layered test strategy in ANDROID-TESTING-ARCHITECTURE.md: unit → domain/repository → ViewModel → Compose UI → navigation → **Firebase Emulator (Security Rules mandatory)** → E2E. Rules tests run in CI; never against production. Mocks only inside tests.

## Why chosen
- RBAC/security must be proven via emulator rule tests (owner/driver/labourer/unauthorized).
- Offline/sync validated against Emulator.
- Every UiState (incl. offline/forbidden/error) covered.
- Catches orphan requirements/unauthorized operations (Phase-0 §45).

## Alternatives
- Manual-only: insufficient for security & regression.
- Unit-only: misses UI/rules/sync.

## Trade-offs / consequences
- Emulator infrastructure in CI (Auth+Firestore+Functions+Storage).
- Test matrix large; prioritize critical journeys + security rules.
- No production data as fixtures; no destructive tests against prod.
