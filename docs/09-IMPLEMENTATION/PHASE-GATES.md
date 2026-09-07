# SAND WORKS — Phase Gates & Quality Gates

Status: **RECONCILED to `docs/10-SANDWORKS/` (locked scope).** Supersedes retired S-V1 gate set. Planning only.

Gates are the sole mechanism to advance work. Each gate has an owner/approver (owner for scope/UX/copy; engineering + test sign-off for technical gates). No task in a later phase begins until its gate is met (unless explicitly parallelised in MASTER-PLAN and gated independently).

## Gate-0 — Spec frozen (EXECUTED)
Exit: `docs/10-SANDWORKS/` 24 docs locked; directives PRODUCT-FREEZE/DIRECTIVE-REGISTER agreed; this 09 plane reconciled to it (this package). R-1 performed. Old S-V1 inventory retired.

## Gate-1 (after P1) — Foundations & identity
- Project builds with package `com.roshan.sandworks`, brand SAND WORKS applied with locked immutable PNGs.
- DI, navigation, design system/theme, unified error + session/conflict handling present and referenced from spec.
- Accessibility/responsive baseline wired (not necessarily final).

## Gate-2 (after P2) — Data & offline
- Room/DataStore schema + outbox present; deterministic replay & idempotency proven (FT-OFFLINE/FT-CONC); optimistic concurrency primitives (`rev`) present; no fake success path.
- Offline reads serve owner/driver/labourer queries.

## Gate-3 (after P3) — Auth, security, RBAC, rules, CF, audit
- **Needs SW-BLK-1 (real Firebase) and, for Storage/CF, SW-BLK-2 (Blaze).**
- Real Auth + approvals server-authoritative; RBAC + org scoping + deep-link authz; Firestore/Storage rules (incl. temp-assignment expiry, storage checks) tested under emulator; CF ops B-01..B-18 implemented & authz/txn/idempotent/audit; attack suite green (FT-SEC). Not fakeable — if creds/plan absent, Gate-3 remains BLOCKED (report, don't fabricate).

## Gate-4 (after P4) — Money engine & scheduling
- Rate snapshot immutable; distribution integer & correct for equal/custom; daily closure exactly-once per (org,date) boundary; leaderboard weekly/monthly deterministic, real data only, no fabricated ranks.
- Blaze-scheduled closure path OR honest Spark fallback chosen per SW-BLK-2.

## Gate-5 / Gate-6 / Gate-7 (after P5/P6/P7) — OWNER / DRIVER / LABOURER surfaces
Each surface task passes the sub-checklist (see Phase-5 tasks.md): sealed UiState, UDF ViewModel (no fake success), repository consumption, real UI behaviour (no inert/placeholder), all truthful states (Loading/Empty/Error/Offline/Submitting/Conflict/Forbidden/Unauthorized/SessionExpired), a11y PASS, responsive/adaptive, tests (happy/empty/loading/error/offline/forbidden+interaction). Screens with cloud-dep (approvals, export, photos, notifications) additionally require Gate-3/8 deps.

## Gate-8 (after P8) — Notifications & alerts
- **Needs SW-BLK-4 (FCM).** Centre + types A–F per-user targeted; owner alert owner-only, strongest-compliant (honest Android limits; no full-volume-override/DND-bypass claims). No private-financial cross-broadcast; approved copy (SW-BLK-5) applied.

## Gate-9 (after P9) — Quality, release, handover
- Test matrix + coverage gate (SW-901); a11y/responsive/i18n audit (SW-902); real-device/perf/offline field test (SW-903, SW-BLK-6); build/signing/release with Play Integrity + V2 keys (SW-904, SW-BLK-3); security/compliance retest (SW-905); release checklist vs DIRECTIVE-REGISTER + blockers, asset discrepancy resolved (SW-BLK-A1/A2), production services verified, handover (SW-906).

## Go-live definition for SAND WORKS
**Spec/planning readiness** is gated by Gate-0 (met). **Code readiness** for non-cloud areas is gated by Gates 1-2 + UI gates. **Go-live (private APK)** is gated by Gate-9 which cannot pass until owner/env blockers SW-BLK-1..6 and A1/A2 clear. These two are reported separately and never conflated.
