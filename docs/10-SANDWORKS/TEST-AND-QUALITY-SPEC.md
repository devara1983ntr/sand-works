# TEST & QUALITY SPEC — SAND WORKS

Quality bar (directive §45): industry-level, production-ready, premium, fast, reliable, accessible, secure. No placeholder data, fake data, fake notifications, fake money, fake leaderboard, fake Firebase responses, fake loading completion, fake success, TODO/FIXME/TBD implementation, dead buttons, inert controls, decorative features that do nothing. Every visible control works; every role tested; every backend rule tested. AGENT.md §14 binds.

## Test categories required (§46 + control)
Authentication · Owner/Driver/Labourer authorization · Approval flow · Trip creation & editing · Multi-driver concurrency · Multi-tractor totals · Rate snapshotting · Money calculation · Daily closure idempotency · Leaderboard calculation · Attendance · Temporary-assignment expiry · Notifications · Owner alert · Export · Offline queue · Sync · Conflict handling · Firestore Security Rules · App Check · Accessibility · Navigation · Dark theme · Light theme · Configuration persistence.

Tooling: Firebase Emulator Suite for backend/rules/CF; JVM unit (domain/money), repository/ViewModel, Compose UI, navigation, a11y, offline/sync, E2E, performance, release.

## Money/domain test emphasis
Integer maths no drift; equal-split remainder; rule snapshots; closure idempotent (run twice → no double); labourer eligibility; leaderboard determinism + no-fake-rank; no "payment" wording regression.

## Security test emphasis
Rules per role × operation × (approval/status/expiry); escalation (labourer write, driver→owner, forge role/approval/money/rate/number/closure/audit/timestamp/org); deep-link auth; storage rules; App Check; disabled/expired; idempotency/conflict.

## UI/UX test emphasis
State coverage per screen (loading/empty/error/offline/submitting/conflict/forbidden/expired); every interactive element tested (no dead/inert); a11y (TalkBack, targets, contrast, scaling, focus, keyboard, reduced motion, colour-independence); responsive breakpoints; light+dark; config persistence.

## Release gate
Green static analysis + tests + security + a11y + perf on a clean machine; SEC-1/signing remediation; no disabled/weakened gate (AGENT §14.19). Private APK distribution only.

## Definition of done (per feature/screen)
Real implementation; real states; validation; persistence/backend integration; authorization; truthful loading/success/failure/offline/conflict; accessibility; tests; no placeholders/fake/dead paths; evidence recorded.
