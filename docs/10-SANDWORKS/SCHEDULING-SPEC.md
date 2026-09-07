# SCHEDULING SPEC — Daily Summary (19:30) — SAND WORKS

## Objective
Daily earning summary produced in the evening. Default 19:30 IST, owner-configurable within the 7–8 PM operational window.

## Authoritative rule
- The daily calculation is **server-authoritative** and **idempotent per (organization, date)**.
- It closes the day's eligible trips, computes each user's accrued amount, records the result, prevents duplicate processing, notifies eligible users, and preserves an auditable record.

## Execution options (honest, per Blaze/Spark)
| Plan | Option | What happens | Honesty requirement |
|---|---|---|---|
| **Blaze** | Cloud Functions scheduled trigger (e.g., Cloud Scheduler→CF or CF `schedule`) at configured time | Truly server-authoritative; no device needed | Full featured path |
| **Blaze** | Cloud Functions on-trigger / callable closure + owner or client invoke | Server-authoritative closure via CF call | Acceptable if scheduled CF unavailable but CF present |
| **Spark** | NO real scheduled execution exists | Do NOT fake it | Documented fallback only (below) |

### Spark fallback (if owner stays on Spark)
- Do NOT claim server-authoritative scheduled processing exists.
- Provide a clearly documented fallback that still enforces the (org,date) idempotency boundary and stays auditable:
  - e.g., an **OWNER "Run daily summary"** action and/or a **client-side reminder at 19:30** that prompts the owner (and optionally a driver) to invoke the closure.
  - Even if invoked from a client, the closure computation is executed by a trusted path (a Cloud Function if available, or a guarded server-validated process) — never a client fabricating totals.
  - Every closure writes `dailyClosures/{org}_{date}` + `earningCalculations` with inputs/outputs; running twice returns the same result (no double money).

## Idempotency
- Closure boundary doc `dailyClosures/{orgId}_{date}`. Transaction + guard; a second run detects the final doc and returns the recorded result. No duplicate earnings.
- Idempotency key on the invocation.

## Notification
- After a successful closure, eligible users receive their **daily earning summary** notification (type A) with locked wording (accrued total, not "payment").

## Configuration
- `settings.summaryTime` (default 19:30 IST), owner-editable within 7–8 PM window; validated (hh:mm IST).

## Tests
- Closure idempotency (run twice → same result, no double), correct day bound, correct participant eligibility, snapshot inputs preserved, notification only to eligible users, time config validation, Spark-fallback path does not fake scheduling and still idempotent/auditable.

## Verification
No fake server-authoritative scheduling. Where the real scheduled trigger is unavailable (Spark), the spec documents the fallback and the UI/ops copy states what actually runs. Mark Blaze-gated scheduling feature as BLOCKED until plan decision (SW-BLK-2) rather than pretending it exists.
