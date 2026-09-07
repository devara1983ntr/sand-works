# End-to-End Flows

Status: Phase 0.5 (PROPOSED). Complete E2E scenarios tying screens+backend+data+auth. Flag any step with no defined screen/backend/data. Each step is traceable to a screen and a backend/DB op.

## E2E-1 Owner records a day's work (core)
1. Owner launches → N-01 splash → session active → role resolve → N-20 dashboard.
2. No session → [Record first trip / + New] → N-24 New Work Session (date today, Morning, type Sand).
   Backend BO-1 (CF create, uniqueness). Success → back to N-20 shows Open session.
3. Add trip → N-25 → pick Tractor A, Driver Raju, Place; Add labourers Ramu/Shyam/Gopal from N-29 catalogue → Save. BO-2 CF (assigns trip #4 server). N-20 updates (counter).
4. Open trip → N-23 → mark Ramu present, Shyam present, Gopal absent → Save attendance. BO-3 (partial-capable) → counts 2/3.
5. Later mark Gopal present correction (reason "arrived noon") → BO-3 correction audited.
6. End of day → Close session (W6, BO-5) → counter final.
7. View Reports N-27 → sees trip/labour-days → Export (BO-14).
   Audit trail available N-36.
All steps defined (screen ✓ backend ✓ data ✓ auth ✓). No gap.

## E2E-2 Driver-assigned trip (gated D-1/D-6)
1. Owner assigns Raju to trip → BO-6 → N-50 assignment + FCM to Raju.
2. Raju logs in (N-03) → driver home N-50 shows assigned.
3. Raju opens N-51, confirms → BO-7 (state) → owner notified; trip shows Accepted.
4. Raju starts → InProgress; completes → Completed; owner sees N-23 status.
5. Owner closes session.
Gap flags: driver screens/BO-7 exist only if D-1/D-6 = YES; currently DECISION-pending. If NO, this flow becomes owner-direct-complete and the "assign/confirm/status" path is removed (not orphaned).

## E2E-3 Offline owner records on reconnect
1. Owner offline → banner → opens N-20 cached → adds trip N-25 (autosave) → queued "pending".
2. Reconnect → outbox syncs BO-2 idempotent → success; conflict if a matching trip exists → Conflict UI (merge/keep). Audit. Never fake success.
Defined; conflict resolution UI + idempotency needed (gaps → register).

## E2E-4 Notifications & account change
1. Owner changes Raju's role → BO-10 → FCM security notification to Raju → tap deep link N-34/N-39.
2. Raju disabled → login shows N-06; no data.
3. Owner makes announcement BO-11 → N-41/FCM → audience reads.

## E2E-5 Backup / restore
Owner N-38 → backup BO-12 → progress → complete (+ notif). Restore BO-13 confirm+rollback+verification (reference semantics). Audit restore.

## E2E-6 Labourer self view (gated D-1)
Labourer login → N-60 see own attendance history → detail N-61; read-only / (optional self-confirm). Own-data scope only.

## Traceability coverage flags
| Step with no defined artifact | Severity |
|---|---|
| Driver status lifecycle (assign/confirm/start/complete) missing unless D-1/D-6 | MED |
| Session "close" UX screen absent in reference (added N-22/W6) | MED |
| Conflict-resolution UI for offline/online & two-admin edits not yet fully speced | HIGH (see CONCURRENCY) |
| Correction "reason" field not yet added to reference schema | HIGH |
| Reports counter/CF pipeline not yet specified at field level | MED (D-7) |

## Rule
Every E2E primary/critical flow's every step must map to (screen, backend op, data op, authz, audit, notification) or the flow is incomplete and flagged in PRODUCT-GAP-REGISTER. Driver/labourer paths DECISION-gated.
