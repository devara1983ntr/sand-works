# FINAL WORK STATE MACHINE — V1 (Phase 0.75)

Scope S-V1: owner-driven. Full driver "Assigned→Accepted→InProgress→Completed" machine is **deferred (D-6)** and NOT modelled in V1. States below are the minimal, justified set. Do not add states without a product reason.

## 1. WorkSession states
```
        ┌───────────────┐   OWNER create   ┌────────────┐   OWNER close   ┌────────────┐
        │ (not existing)│ ───────────────► │   OPEN     │ ──────────────► │   CLOSED   │
        └───────────────┘                  └────────────┘                 └────────────┘
   (unique org,date,session)                 add trips / edit /            immutable-ish;
                                             attendance record             corrections limited +
                                                                           reason + audit (R-32)
```
Transitions:
- OPEN (create): actor OWNER; pre none; validation uniqueness (R-01); mutation workSessions create (CF); authz AUTHZ-OWNER; audit AUD; offline queued; concurrency uniqueness transaction.
- OPEN → CLOSED (close): actor OWNER; pre OPEN; validation no concurrent close; mutation status=closed + final counter (CF transaction); audit close; notification none; offline blocked (server-authoritative, online required) → queue-and-replay via CF; retry idempotent; concurrency conflict handled (one wins; other shown "already closed").

## 2. Trip states (owner-driven, minimal)
```
 OPEN session ─ add ─► RECORDED/ACTIVE ─ owner edit/delete(soft) within session
                        │
                        └ (no separate driver states in V1)
```
Trip status enum in V1: `RECORDED` only? No — retain a real status for integrity but keep minimal: status ∈ {draft? , recorded}. Because reference trip had a status string with no real lifecycle, and driver workflow is deferred, V1 needs **no user-facing trip status transitions** beyond being part of an open/closed session. Decision: V1 does NOT expose a trip-level status state machine; immutability is governed by the **session state** (open/closed) + record deletion rules. This removes an unnecessary state machine (per "do not include unnecessary states").
- Rationale: single owner records a trip and attendance immediately; there is no intermediate assignment/acceptance actor in V1. Introducing per-trip planned/in-progress/completed states would be fabricated work without a driver actor.

## 3. Attendance record states
```
 Recorded(present/absent) ─(OWNER correction, reason, audit)─► Corrected (history kept)
```
No silent overwrite; each correction appends a versioned audit record with original + corrected + reason + actor + timestamp. "Confirmed" (driver/labourer) state deferred (D-1).

## 4. Labourer/Driver catalogue states
```
 Active ─(owner soft-delete/inactivate)─► Inactive  (history preserved; not assignable to new trips)
```

## 5. User/account states (V1 owner)
```
 Provisioned(active OWNER) ─(CF disable/suspend)─► Suspended ⇄ Active ; account delete via CF w/ audit
```

## Transition template (applies to every transition above)
Who · Preconditions · Validation · DB mutation · Authorization · Notification · Audit event · Offline behaviour · Retry · Concurrency — completed for each transition in the rows above; missing ones default to: Notification none (no cross-role in V1); Offline = queue idempotent (or online-required where server-authoritative); Retry idempotent (opId); Concurrency per CONCURRENCY-SPECIFICATION (rev + transaction).

## Deferred state machine (D-6) — reference only, NOT implemented in V1
```
Draft → Assigned → Accepted → InProgress → Completed ; + Cancelled/Rejected
```
Recorded here so the D-6 decision has a ready machine; out of V1 scope.

## Verification
V1 machine has no unnecessary states; each transition maps to a rule + unit test (FINAL-TEST-CONTRACT). If V1 later adds driver states, re-run this section.
