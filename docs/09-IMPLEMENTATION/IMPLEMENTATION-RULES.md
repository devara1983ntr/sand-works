# SAND WORKS — Implementation Rules (control plane)

Status: **RECONCILED to `docs/03-ENGINEERING/AGENT.md` §14 + `docs/10-SANDWORKS`.** Supersedes retired S-V1 rules. Planning-only plane; rules bind execution.

## Authority order (repeated from README, non-negotiable)
1. Owner decisions → `10-SANDWORKS/DIRECTIVE-REGISTER.md`, `PRODUCT-FREEZE.md`
2. SAND WORKS spec → `10-SANDWORKS/*`
3. Binding rules → `03-ENGINEERING/AGENT.md` §14 (immutable)
4. Reference continuity → `08-NATIVE-ANDROID/` (non-conflicting)
5. Flutter `lib/` → behavioural reference only
6. Agent inference → never

## Hard rules
1. **No fabrication** of Firebase project, FCM, Storage, Cloud Functions, signing, or any infra (AGENT §14.4/14.5). Author & emulator-verify now; **never claim live/real-cloud DONE** without creds. Emulator-first: no SW task is blocked at authoring (see PROGRESS-TRACKER); real-cloud/release DONE waits on SW-BLK-1..6/A1/A2 per the two-tier matrix.
2. **No fake success / silent failure** offline; expose pending, sync, conflict truthfully (AGENT §14.6).
3. **Roles OWNER/DRIVER/LABOURER** (no ADMIN). Labourer operationally **read-only**. Remove all retired single-owner assumptions.
4. **Server-authoritative** for privileged/money/numbering/approval/audit/expiry; client never self-awards.
5. **Idempotency** everywhere money/closure writes; exactly-once per (org,date) for closure. Integer paise; no floating point money.
6. **Accrued-totals wording only** — never "payment/paid"; leadership, export, notifications all respect this.
7. **Immutable brand assets** — locked PNGs only, no SVG/regeneration/redraw; canonical set per SW-BLK-A1/A2 (do not guess).
8. **Package `com.roshan.sandworks`; START FRESH** (no legacy migration). No public Play release.
9. **Approval before access** for driver/labourer; temp-assignment expiry backend-enforced.
10. **Privacy & permissions**: no broad notification request; no full-volume/DND-bypass claims; no PII analytics.
11. **No private-financial cross-broadcast** of notifications between users.
12. **Blocker governance**: business/design/asset blockers only owner-cleared; never disguised as tasks; spec vs go-live kept separate.
13. **Gates gate progress** (PHASE-GATES.md); a phase/area cannot pass on an un-cleared blocker.
14. **Traceability** — every task maps to a real requirement; every requirement to tasks/screens/gates (TRACEABILITY-MATRIX). No orphan tasks/features.
15. Secrecy — no keys/creds committed; signing in env/ignored.
