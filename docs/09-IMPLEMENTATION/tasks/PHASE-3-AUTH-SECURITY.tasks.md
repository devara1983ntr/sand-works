# Phase 3 — Auth, Approvals, RBAC, Rules, Cloud Functions, Audit — Task Contracts

Phase objective: real Firebase auth + owner provisioning + driver/labourer registration & approval + backend-authoritative RBAC/org scoping + Firestore/Storage Rules + Cloud Functions (B-01..B-18) + audit + temp-assignment expiry. **Requires real Firebase env (SW-BLK-1) and Blaze for CF/Storage (SW-BLK-2).** Do NOT fake any of it (AGENT §14.4/14.5).
Shared refs: `10-SANDWORKS/SECURITY-RBAC.md`, `BACKEND-OPERATIONS.md`, `DIRECTIVE-REGISTER.md`, `DATA-MODEL.md`, `NOTIFICATION-ALERT-SPEC.md`, `CONCURRENCY-IDEMPOTENCY-AUDIT.md`.

### SW-301 Firebase project bootstrap + App Check
Status: NOT-STARTED (implementable: scaffold config + emulator wiring now). DONE/real-cloud verify requires **SW-BLK-1** (real project) + SW-BLK-2 (Play Integrity App Check/cloud). No fake App Check. Priority P0. Type: backend.
Objective: wire approved Firebase services (Auth/Firestore/Storage/Functions/FCM/App Check/Crashlytics; only per `10-SANDWORKS` + `08-NATIVE-ANDROID/FIREBASE-COVERAGE-MATRIX.md`). App Check (Play Integrity) on. No hardcoded keys.
Tests: later rules/App Check. Downstream: SW-302..309.

### SW-302 Auth integration + session handling (real)
Title: Email/password auth, sign-out/reset, session, session-expiry (preserve outbox/drafts), disabled/suspended handling. No fake auth/role.
Source: `FEATURE-CATALOG.md` SWF-01; `SCREEN-STATE-CONTRACT.md`. Security: server status each op; never trust client role. Tests: FT-AUTH. Downstream: SW-303..305, phase 5-7 auth screens.

### SW-303 Owner provisioning (CF B-01)
Title: provision org + owner user (role OWNER, active) + role claim; never self-role-choice.
Source: `BACKEND-OPERATIONS.md` B-01; `SECURITY-RBAC.md`. Security: allowlist/secret; rate-limited; owner identity is a parameter. Tests: CF emulator. Downstream: phase-5 owner first-run.

### SW-304 Driver/labourer registration + approval (B-02/B-03)
Title: register driver/labourer (approval=pending), OWNER approve/reject before privileged access. Server-authoritative.
Source: `FEATURE-CATALOG.md` SWF-02; `ROLE-AND-USER-MODEL.md`; `BACKEND-OPERATIONS.md` B-02/03. Security: no approved-access until approved; approval never client-writable. Tests: approval flow (FT). Downstream: phase-5 approvals UI.

### SW-305 RBAC client gating + org scoping + deep-link auth
Title: Compose routes hide by role; org/ownership scoping; deep links re-validate target auth+org+ownership+existence → NotFound/Forbidden fallback. UI never authorization.
Source: `NAVIGATION.md`, `SECURITY-RBAC.md`, `FINAL-RBAC` agreement. Tests: FT-RBAC/nav. Downstream: all screens.

### SW-306 Firestore + Storage Security Rules + emulator tests
Title: rules per role × operation × approval/status/expiry; org equality; deleted/soft; deny client writes to server-authority/audit; Storage owner/self + size/MIME/dimension; temp-assignment expiry via server time; query rule-compat (rules are not filters); index declarations.
Source: `SECURITY-RBAC.md`, `BACKEND-OPERATIONS.md` (queries), `10-SANDWORKS` query compliance, `08-NATIVE-ANDROID/FINAL-QUERY-RULE-MATRIX.md` pattern. Tests: FT-RULES (escalation/ownership/forge/expiry/storage/timestamp). Downstream: all writes. Blockers: SW-BLK-1/2.

### SW-307 Cloud Functions B-01..B-18
Title: implement CF ops with authz, txn, idempotency, audit (create trip/number, approval, rate/rule, closure, leaderboard, assignment, alert, notifications, export, profile photo, account ops, etc.). Idempotency + CF-retry-safe.
Source: `BACKEND-OPERATIONS.md` B-01..18; `CONCURRENCY-IDEMPOTENCY-AUDIT.md`. Tests: CF emulator per op. Blockers: SW-BLK-1/2. Downstream: phases 4-8 writes.

### SW-308 Server-authority + audit integration
Title: enforce SERVER-AUTHORITY (role/approval/status/org/timestamps/audit/money/rate/number/closure); server-generated audit; rules deny client audit writes.
Source: `CONCURRENCY-IDEMPOTENCY-AUDIT.md`, `SECURITY-RBAC.md`, `DATA-MODEL.md`. Tests: FT-AUD (forge reject). Downstream: all.

### SW-309 Security attack test coverage (AT)
Title: run emulator security suite covering the attack matrix (role escalation, labourer write, cross-org, forge role/approval/money/number/closure/audit/timestamp, deep-link authz, storage, disabled/expired, idempotency).
Source: `SECURITY-RBAC.md`, `10-SANDWORKS` threat notes, `08-NATIVE-ANDROID/SECURITY-ATTACK-REVIEW.md`. Tests: FT-SEC. Downstream: release.
