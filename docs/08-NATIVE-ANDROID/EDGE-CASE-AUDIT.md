# Edge Case Audit

Status: Phase 0.5 (PROPOSED). Every relevant runtime edge case with expected behaviour, owner, mitigation, test. Extends audit ANDROID-EDGE-CASES (reference) to native + server reality.

| # | Edge case | Expected behaviour | Owner | Mitigation | Test |
|---|---|---|---|---|---|
| E1 | Double-tap submit (create/save/delete) | single op only | all | disable while Submitting + idempotency key | UI test |
| E2 | Tap-while-loading | ignored or queued not duplicate | all | guard states | UI test |
| E3 | Network disappears during submit | no fake success; op queued; retried | all | outbox + connectivity | integration |
| E4 | Backend (CF) succeeds but notification fails | write persisted; notify retried server-side; owner informed | owner | server retry + NOTIFICATION-COVERAGE | integration |
| E5 | Notification succeeds but UI fails | idempotent deep link; user can still navigate | all | deep-link valid/NotFound handling | UI test |
| E6 | Logout during an in-flight op | op either completes (idempotent) or is requeued w/ consent; no partial confusion | all | cancel/requeue semantics | integration |
| E7 | Account disabled mid-session | next action → disabled notice; sign out | all | server status check each op | integration |
| E8 | Record deleted-by-other while viewing/editing | detect on save → NotFound/conflict UI, refresh list | owner | optimistic concurrency | integration |
| E9 | Two users edit same session/trip | conflict detected → merge/reload prompt; no lost update | owner | `rev` optimistic concurrency | integration |
| E10 | Device restart / app kill / process recreate | drafts + outbox restored; state preserved | all | Room + SavedStateHandle | test |
| E11 | Back mid-flow | autosave or confirm-discard; never corrupt | all | dirty handling | UI test |
| E12 | Partial form filled then leave | autosave draft (reference behaviour) | all | draft store | UI test |
| E13 | Malformed server/Firestore response | typed BackendError, not crash | all | mapping + sentry | unit |
| E14 | Missing Android permission | rationale + settings (no broad request) | all | ANDROID-PERMISSIONS | UI/unit |
| E15 | Firestore query would violate rules | design query rule-valid; else Forbidden not silent | all | rules-aligned queries | emulator |
| E16 | Offline write conflicts with online change | conflict UI resolution, no overwrite | owner | reconciliation | integration |
| E17 | Session-number duplicate (concurrent/offline) | server reassigns/rejects; user fixes | owner | CF authoritative numbering | integration |
| E18 | Empty roster when opening attendance | allow add; no save w/o ≥1 labour | owner | validation | UI test |
| E19 | Very large roster/day (perf) | responsive, paginated, report via CF | owner | perf guard (PERF) | load test |
| E20 | Rate-limited auth/announcement | backoff + message | all | RateLimited error | unit |
| E21 | Deleted labourer still referenced by old attendance | attendance history preserved; display "removed" | owner | soft-delete + denorm snapshot | integration |
| E22 | Screen reader + gestures conflict | a11y alternative always present | all | ACCESSIBILITY | a11y test |
| E23 | Session expiry during offline queue flush | re-auth then reconcile outbox; never drop | all | OFFLINE/security | integration |
| E24 | Time/datezone boundary (session rollover) | consistent session boundary (D-5) | owner | fixed boundary | unit |
| E25 | Concurrent attendance toggling on two devices | rev conflict on save | owner | optimistic concurrency | integration |

## Critical-flow guarantee
Each of these has a defined owner, mitigation, and test hook; none is a silent code path. Cases without a decision (e.g. driver-specific offline assignment) are marked and routed to PRODUCT-GAP-REGISTER as DECISION-gated rather than hidden.
