# Phase 4 — Money Engine, Scheduling, Leaderboards — Task Contracts

Phase objective: rate snapshotting, money distribution, daily closure/earnings (idempotent), leaderboards. Domain logic in SW-108; server authority in P3; this phase integrates + verifies. Closure cloud-scheduling gated by Blaze (SW-BLK-2).
Shared refs: `10-SANDWORKS/MONEY-ENGINE-SPEC.md`, `SCHEDULING-SPEC.md`, `WORKFLOW-STATE-MACHINES.md`, `BACKEND-OPERATIONS.md` (B-07..10), `DATA-MODEL.md`.

### SW-401 Rate config + per-trip snapshot (B-07)
Title: OWNER set default ₹200 + change rate; every trip snapshots applicable rate; future rate never rewrites historical trips; integer paise.
Source: SWF-09, MONEY-ENGINE-SPEC §2. Rules: R default ₹200; immutable snapshot. Tests: snapshot immutability. Downstream: SW-403, trips.

### SW-402 Money-rule config + distribution (B-08)
Title: OWNER configures distribution rule (equal default among driver+eligible labourers; configurable equal/driver+labour/custom %/fixed); integer remainder policy; rule snapshot per closure.
Source: SWF-10, MONEY-ENGINE-SPEC §4. Tests: distribution math, configurable rules, eligibility. Downstream: SW-403.

### SW-403 Daily closure/earnings engine + scheduling (B-09)
Title: daily summary default 19:30 IST (configurable 7-8 PM): snapshot eligible trips, compute per-user accrued (integer), write earningCalculations + earnings (append, immutable) + dailyClosures/{org}_{date} idempotent boundary, notify eligible (A), audit. **Server-side if Blaze; honest Spark fallback** (owner/device-triggered closure, still idempotent+auditable; never fake scheduled authority).
Source: SWF-11/12, MONEY-ENGINE-SPEC §6-7, SCHEDULING-SPEC, WORKFLOW §4. Rules: exactly-once; no "payment" wording. Tests: closure idempotency (run twice→no double), correct day bound, eligibility, fallback honesty. Blockers: SW-BLK-2 for Blaze scheduling path (fallback still built). Downstream: SW-404, notifications.

### SW-404 Leaderboards weekly/monthly (B-10)
Title: weekly (reset each week) + monthly (reset each month), top-3 only, deterministic tie-break, from real persisted trips; if fewer eligible → only real ranks (never fabricate 2nd/3rd).
Source: SWF-13, MONEY-ENGINE-SPEC §8. Tests: determinism, no-fake-rank, reset boundaries. Downstream: labourer/owner leaderboard UI.
