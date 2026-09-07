# MONEY ENGINE — SAND WORKS

Covers rate snapshotting, distribution, earnings accrual, daily closure idempotency. Integer currency (paise) throughout — no floating-point. All server-authoritative. "Accrued totals", never "payment".

## 1. Currency representation
- All money stored/computed as **integer paise** (₹1 = 100 paise). Display formatting to ₹ happens only at the UI boundary. No float, no rounding drift. Deterministic integer arithmetic.

## 2. Rate snapshotting (§10, SW-2)
- `settings.defaultRate` = ₹200 (20000 paise) at start.
- OWNER changes `settings.defaultRate`/adds `rateSnapshots{rate, effectiveFrom}` via CF.
- On trip record, the trip captures an **immutable `rateSnapshot`** = the rate effective at record time.
- Changing the future rate NEVER rewrites historical trips. Historical trip total = tripCountSnapshot × historical rateSnapshot.
- Audit every rate change.

## 3. Trip total (§11)
- totalTrips (per trip = 1 for a single trip; a "trip count" is a count of trip records on a day/tractor/driver) — the directive's `totalTrips × applicableTripRate` refers to aggregate counting. Per-record basis: each trip contributes `rateSnapshot`.
- Day/driver/tractor/org totals are **sums over trip records** with their own snapshots; never recomputed against today's rate.

## 4. Distribution (§11, SW-3)
- Distribution is **per trip** among participants: driver + labourers **present/eligible** on that trip.
- Default rule: **equal split** → each of (1 driver + N eligible labourers) gets `tripValue / (1+N)` in paise with a documented integer remainder policy (deterministic; remainder handling recorded in the calculation so it is auditable and reproducible).
- Configurable rule options (owner, via settings.moneyRule + snapshots): equal split / driver-share + labour-share / custom percentage / custom fixed allocation.
- The rule active at closure is snapshotted into `earningCalculations` (immutable) so historical results are reproducible.
- Rule changes do not retroactively change past closures.

## 5. Accrual & daily summary (§12/§13/§12 SW-4/5)
- Daily summary default 19:30 IST, configurable within 7–8 PM window.
- Purpose: close the day's eligible trip calculation, compute each eligible user's accrued amount, record result, dedupe, notify eligible users, preserve auditable record.
- Accrued totals only — physical payment is outside app scope. UI wording: "Today's earnings added"/"Today's trip earnings summary". NEVER "Payment completed".

## 6. Daily closure idempotency (§38)
- Closure boundary = **(date + organization)** — a `dailyClosures/{orgId}_{date}` doc.
- Closure runs exactly once for a given (org,date). A retry must not double money.
- Idempotency: if a closure doc exists and is final, a re-run returns the recorded result (no recompute/duplicate). Use transaction + guard doc + idempotency key.
- OWNER can inspect the calculation (inputs/outputs in `earningCalculations`).

## 7. Server vs client scheduling honesty (§12, SW-BLK-2)
- Authoritative daily calculation should be **server-side (Cloud Functions scheduled trigger)** when on **Firebase Blaze**.
- **If the project stays on Spark:** do NOT fake server-side scheduled execution. Provide a clearly documented fallback (e.g., owner/device-triggered closure that calls a Cloud Function at 19:30, or an explicit manual "Run daily summary" owner action) that still enforces the (org,date) idempotency boundary and remains auditable. Do not claim server-authoritative scheduled processing exists when it does not.
- System remains idempotent regardless of trigger path.

## 8. Leaderboard determinism (§15, SW-10)
- Weekly leaderboard resets each week; monthly resets each month. Top 3 only (1st/2nd/3rd).
- Tie handling deterministic (documented tie-break key, e.g., earlier total achieved / fewer trips / stable id ordering).
- Based on actual persisted trip/earning data. If only one eligible worker has trips, show only that real rank — **never fabricate 2nd/3rd**.
- Derived server-side or via CF on closure; recomputed from real data.

## 9. Tests
Money engine tests: integer maths (no drift), rate snapshot immutability, equal-split remainder policy, configurable rules, daily closure idempotency (run twice → no double), labourer absent/eligible correctness, leaderboard determinism + no-fake-rank, negative/invalid inputs, corrections.

## 10. Verification
All figures real; no fabricated money, no "payment" wording, no silent historical change. Audit each financial operation (moneyRule/rate change, closure, correction).
