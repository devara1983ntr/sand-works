# Workflow Specification

Status: Phase 0.5 (PROPOSED). Each business workflow modelled Trigger→Precondition→Action→Validation→Authorization→Transition→Mutation→Side effect→Notification→UI→Audit→Completion. Identifies missing steps.

## W1 Create WorkSession
Trigger: owner starts a day/session. Precond: active owner; no conflicting open session (org,date,session). 
Steps: open N-20→N-24 → fill date/session/worktype → save.
Validation: date valid; session in allowed set (D-5); uniqueness (CF). Authz: owner/admin. Transition: draft→open. Mutation: workSessions create. Side effect: none. Notification: none. Audit: created. Completion: dashboard shows session.
Missing-step flag: uniqueness race on two devices → must be enforced by transaction/CF + conflict UI (see CONCURRENCY/DATA-CONSISTENCY).

## W2 Add Trip
Trigger: owner adds a trip to a session. Precond: session open. 
Steps: N-25 → tractor/driver → labour roster → attendance → save.
Validation: driver required; ≥1 labour; attendance subset of roster; status valid. Authz owner. Transition: session accumulates trip; number = max+1 server-authoritative. Mutation: trip + attendance. Side effect: none (or notify driver if assigned D-6). Audit: creation. Completion: refresh.

## W3 Assign to driver (gated D-1/D-6)
Trigger: owner assigns a driver to a trip/job. Precond: active driver.
Authz: owner. Transition: trip.assignedDriverId set. Mutation + notification to driver + audit. Missing-step flag: reassignment & driver-offline handling; driver acknowledgement semantics (D-6) undefined → REQUIRES DECISION.

## W4 Driver status update (gated D-1/D-6)
Trigger: driver confirms/updates own trip. Authz: CF validates state + own-assignment. Transition by state machine (see DATABASE state transitions). Notification to owner. Audit. Missing: full state machine (planned/accepted/in-progress/completed/cancelled) depends on D-6 scope.

## W5 Attendance record/correction
Trigger: owner records attendance; correction later. Authz: owner records; corrections audited. Mutation: attendance versioned. Missing-step flag: no "undo/correction reason" field today → add reason (owner) + audit before/after.

## W6 Session close
Trigger: end of day. Authz owner. Transition open→closed; closed largely immutable (owner override audited, restricted window D-8). Missing: explicit close action/screen (reference had no close). Add to N-22/N-20.

## W7 Work assignment lifecycle summary (aggregate)
Draft → Open → (Assign→Confirm→InProgress→Completed | Cancelled) — pending D-6 for driver legs; otherwise owner records completed directly.

## W8 Crew/user management
Owner CRUD driver/labourer; owner CF create user/role/status; audit.

## W9 Backup/restore
Export/import with validation+rollback+verification (reference). Cloud backup via CF.

## W10 Reports
Period aggregation. Missing-step flag: expensive client aggregation → use CF/scheduled + counters to avoid full-history load (reference perf risk PERF-2).

## Missing workflow steps register
| Workflow | Missing step | Severity | Fix |
|---|---|---|---|
| W1/W2 | Concurrent/offline duplicate prevention | HIGH | transaction/CF + idempotency |
| W3/W4 | Driver acceptance/state machine semantics | MED | D-6 decision |
| W5 | Correction reason + versioned attendance | HIGH | add fields + audit |
| W6 | Session close & immutability window | MED | add op + rule |
| W10 | Report aggregation performance | MED | CF/scheduled/counters |

## Verification
All workflows PROPOSED; missing steps feed PRODUCT-GAP-REGISTER; gated rows D-1/D-6/D-8. State machines in DATABASE-COMPLETENESS + CONCURRENCY-AUDIT.
