# ADR-005: UDF / MVI-style State Model

## Context
Flutter used BLoC with sealed states; native needs explicit, testable, one-way state without arbitrary global mutable state.

## Decision
Use **UDF/MVI-style** state management: UI emits intents/events → ViewModel runs use cases → exposes `StateFlow<UiState>` (sealed). States: Initial/Loading/Success/Empty/Error/Offline/Unauthorized/Forbidden/Refreshing/Submitting/SuccessWithStaleData. Navigation/one-shot events handled explicitly (ADR-013), not as data state where avoidable.

## Why chosen
- Predictable, single-source-of-truth per screen.
- `collectAsStateWithLifecycle` lifecycle-aware.
- Sealed UiState = exhaustive, recoverable handling.
- Testable ViewModels.

## Alternatives
- Plain mutable MutableState everywhere: error-prone, no explicit transitions.
- Redux: boilerplate-heavy; not needed.
- Single global store: unnecessary.

## Trade-offs / consequences
- ViewModels per screen; discipline to keep state in sealed classes; explicit loading/offline/forbidden states required by error-state docs.
- Mapping handled in ViewModel/domain.
