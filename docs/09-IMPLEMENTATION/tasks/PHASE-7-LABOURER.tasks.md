# Phase 7 — LABOURER Surfaces — Task Contracts

Phase objective: complete LABOURER app surfaces (READ-ONLY operationally) per `10-SANDWORKS/SCREEN-CATALOG.md`. Mandatory sub-checklist as Phase 5. Prereq phases 1-4. Execution gated by Gate-0/env.

### SW-701 Labourer shell + nav (read-only)
Labourer Home/More; own scope only; no write actions exposed (and none authorized). Source: NAVIGATION/SCREEN-CATALOG.

### SW-702 Labourer dashboard (personal metrics, read-only)
TOTAL TRIPS, TOTAL EARNED, REMAINING MONEY, WORKING DAYS, ABSENT DAYS, WORKING DATES, ABSENT DATES; weekly+monthly rank; weekly trip total. Source: SWF-05/12; SWF-13. Derived from own persisted data only. Empty = truthful zero/empty, never fabricated.

### SW-703 Labourer working/absent history + leaderboard view
Date-based own history; weekly/monthly leaderboard top-3 (own position). Source: SWF-05/13. Real data only; no fake ranks.

### SW-704 Labourer profile + notifications (own)
Profile (picture via Storage if Blaze) + own targeted notifications (daily earnings summary wording = accrued, not "payment"). Source: SWF-14/16/12. Read own.
