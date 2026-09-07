# Test Coverage Matrix

Status: Phase 0.5 (PROPOSED). Feature × Role × scenario (Happy/Validation/Error/Offline/Security/Accessibility). No critical feature empty. Extends ANDROID-TESTING-ARCHITECTURE.

## Layers (as per testing architecture)
- Unit: usecase/domain, rules, validation, numbering, state machine, concurrency.
- Repository: Room outbox, mapping, retry/idempotency, offline queue.
- Compose UI: each screen Happy/Empty/Loading/Error/Offline/Forbidden + interaction + nav/back.
- Integration + Emulator: Firestore rules authz, CF ops, FCM, audit, concurrency, data-consistency.
- Accessibility: TalkBack semantics, contrast, touch-target, text-scaling, keyboard.
- Security: rules escalation, ownership, delete vuln, audit forge attempt.

## Feature × Role × scenario coverage matrix
Fill status: C=complete-strategy, P=partial/needs-DECISION(D-1/D-6), L=low.
| Feature | Owner | Driver | Labourer |
|---|---|---|---|
| Auth/login/recovery | C | C | C |
| Dashboard/today | C Happy/Empty/Error/Offline | P (D-1) | n/a |
| Session/trip create-edit | C + Validation/Conflict/Offline/Concurrency | n/a | n/a |
| Attendance record/correct | C + Partial failure + Versioning | P (D-1 confirm) | P self (D-1) |
| Delete trip (soft) | C + confirm/undo/audit | n/a | n/a |
| Session close | C + immutability | n/a | n/a |
| Crew mgmt | C + validation | read | read |
| Users/roles (owner) | C + security(role/escalation/≥1 owner) | n/a | n/a |
| Reports | C + empty/export/perf | P own-summary | n/a |
| Notifications | C + permission/read/deep-link | C | C |
| Backup/restore | C + rollback | n/a | n/a |
| Audit viewer | C (owner/admin) | n/a | n/a |
| Offline queue/sync | C + conflict/resolution | C | C |
| Profile/account | C | C | C |
| Driver work flow | n/a | P (D-1/D-6) | n/a |
| Labourer attendance view | n/a | n/a | P (D-1) |

## Cross-cutting scenario strategy (examples — all features)
| Scenario | Strategy | Status |
|---|---|---|
| Happy path E2E (E2E-1) | full integration | C |
| Validation each field | unit + UI | C |
| Offline write→sync | outbox + WorkManager integration | C |
| Rules authz each cell | emulator rules tests (UI matrix == rules matrix) | C |
| Concurrency (rev, idempotency, double-submit) | repository/CF tests | C |
| Data-consistency (restore rollback, counter drift) | integration | C |
| a11y per screen | TalkBack/semantics + contrast + scale | C |
| Security escalation/ownership/delete | dedicated security suite | C |
| Error→retry idempotent | UI + repo | C |

## Rule
No critical feature has an empty row: every (feature×role) either has a concrete test strategy or is explicitly gated (D-1/D-6) with the intended strategy stated. Gated rows are PENDING-DECISION, not silently missing.
