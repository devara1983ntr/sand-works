# Data Consistency Audit

Status: Phase 0.5 (PROPOSED). Multi-record workflows — atomic vs eventually-consistent vs compensating vs rollback vs retry. Do not assume Firestore makes multi-step workflows atomic.

## Classification of workflows
| Workflow | Steps span | Model | Mechanism |
|---|---|---|---|
| Create session | 1 write | atomic | single doc |
| Create trip + counter | 2 docs | atomic/eventual | CF transaction (counter + trip) OR single-owner trip write + CF counter recompute |
| Attendance batch (many rows) | N rows | eventually-consistent | batched writes are atomic on write but roll-ups are async → counter eventually-consistent |
| Delete trip + cascade attendance | trip + N attendance | compensating/soft | soft-delete trip (atomic); attendance marked deleted via CF in batches (eventual); verify |
| Assign driver + notify | trip + notification | compensating | trip update + notify; if notify fails, trip state stays consistent (retry notify) — partial side effect ok |
| Session close + final counter | session + counter | atomic | CF transaction |
| Role change + user doc + audit + notify | multiple | atomic (core) + async notify | user doc + audit atomic via CF; notify async |
| Restore (BO-13) | many docs | rollback-required | multi-step: validate backup → apply in dependency order → verify → on failure rollback to snapshot; one-shot id |
| Report counter aggregation | counters | eventually-consistent | CF/scheduled recompute; read shows last-good + "updating" |
| Denormalised displayName rename propagation | many refs | eventually-consistent | CF updates references; until done show old snapshot |
| Backup | copies all | consistent snapshot | take consistent export; label version |

## Consistency invariants
- Multi-step workflows are NOT assumed atomic just because Firestore batch-writes are: cross-collection invariants and counters need CF transactions or async reconcilers + verification.
- Where eventual consistency is acceptable (counters, report aggregates, denormalised names, notification side effects) define the staleness window and a reconciler; never present eventual data as real-time authoritative when it isn't.
- Where a strict invariant exists (unique open session, sequential numbers, ≥1 active owner, role writes) require atomic/transactional enforcement and fail closed.
- Compensating actions for side effects (notify/email) retry independently without corrupting the source of truth.
- Restore/import is the one place full rollback to a snapshot is required; all other ops prefer forward-reconciliation (idempotent re-apply) over destructive rollback.

## Per-op gap register
| Op | Consistency gap if naive | Fix |
|---|---|---|
| Delete cascade | partial attendance delete → orphan inconsistent | soft-delete batches + verify + audit |
| Counter/attendance roll-up | counter drift after partial failure/offline | CF reconciler + recompute on reconciliation |
| Report aggregates | drift if derived from live full-history each query (perf+staleness) | CF/scheduled aggregate + counters |
| Number assignment | race under concurrency | transaction + CF re-derivation |
| Restore | partial apply leaves mixed state | snapshot rollback + verification |
| Denormalised names | stale names in old records | propagation + display fallback |

## Verification
PROPOSED; consistency scenarios in DATA-CONSISTENCY tests (integration + emulator) verifying invariants after simulated failures. Flag each required reconciler/transaction as a backend need (see BACKEND-COMPLETENESS) — a senior team could otherwise assume atomicity wrongly.
