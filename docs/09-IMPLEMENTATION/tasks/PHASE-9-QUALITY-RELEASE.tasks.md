# Phase 9 — Quality Gates & Release — Task Contracts

Phase objective: exhaustive test matrix, security retest, assets, hardening, release readiness. **Requires real credentials/signing (SW-BLK-1/5/6).** Each task maps to a Gate / release check.
Shared refs: `10-SANDWORKS/TEST-AND-QUALITY-SPEC.md`, `10-SANDWORKS/ACCESSIBILITY-SPEC.md`, `SCREEN-CATALOG.md` (assets), `DIRECTIVE-REGISTER.md`.

### SW-901 Full test matrix execution + coverage gate
All UI (per screen states), interaction, repository, sync/offline/idempotency, concurrency, rules, CF emulator, security/attack suites. Coverage gate per TEST-AND-QUALITY-SPEC; report per feature → test. 
Tests: entire matrix.

### SW-902 Accessibility + responsive + internationalization audit
PASS accessibility (contrast, touch 48dp, TalkBack semantic, reduced motion, no color-only), responsive 6.3-7", a11y string audit (Hindi/Odia where contractual), text-scale. 
Tests: AT/semantics (MAIN-013).

### SW-903 Real device matrix + performance + offline field test
Real low-end Android field test: offline-first cold start, sync stability under flaky net, orientation/process death/back, deep-link/authz, crash reporting (Crashlytics) if available.
Blockers: SW-BLK-6 (real device/physical burn-in).

### SW-904 Build, signing, Play release (Play Integrity + V2 keys)
Configured signing + release build + Play deploy (Play Integrity). 
Blockers: SW-BLK-1/5 (signing); SW-BLK-2 (services on Play). Release check.

### SW-905 Final RBAC/Security + compliance + attack retest
Retest attack matrix on release candidate: escalation, cross-org, forge (role/approval/money/number/closure/audit/timestamp), storage, deep-link, disabled/expired, idempotency; accessibility/consent/notification limits; regulatory (₹ exactness; no "payment" claim); App Check enforced. 
Tests: full security/compliance sign-off. Blockers: env-dependent subset.

### SW-906 Release readiness + handover + close-out
Release checklist vs DIRECTIVE-REGISTER/BLOCKERS; acceptance criteria (roles O+D+L per SWF); production-provisioned services (auth, App Check, rules, CF, FCM, storage, analytics, Crashlytics) verified; launch account + ownership (Google Play developer, domain/email) verified; asset discrepancy (SW-BLK-A2) resolved before claiming "all assets final"; close-out report; handover to maintainers. 
Blockers: all env/go-live blockers (SW-BLK-1/2/4/5/6); SW-BLK-A2.
