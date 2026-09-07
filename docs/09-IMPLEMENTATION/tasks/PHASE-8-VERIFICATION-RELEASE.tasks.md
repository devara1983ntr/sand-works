# Phase 8 — Verification & Release — Task Contracts

Prerequisites: Phase 7 + all earlier. Execution BLOCKED until READY + all infra present. These are release-level gates, not afterthoughts.

Shared refs: `FINAL-TEST-CONTRACT.md`, `FINAL-PERFORMANCE-CONTRACT.md`, `08-NATIVE-ANDROID/IMPLEMENTATION-ROADMAP.md`, `docs/06-QUALITY/PRE-RELEASE.md`, `CI-CD-ARCHITECTURE.md`, AGENT §14.

### IMPL-801 Full test contract execution
Objective: execute all 17 FINAL-TEST-CONTRACT suites (unit/domain/repository/ViewModel/Compose UI/navigation/Firebase Emulator/Security Rules/Auth/RBAC/offline/sync/conflict/notifications/a11y/localization/performance/E2E/release). Every critical feature verified.
Source: `FINAL-TEST-CONTRACT.md`, `FINAL-TRACEABILITY-MATRIX.md`.
Tests: entire FT catalogue. Acceptance: no critical feature empty; no disabled/weakened gate (AGENT §14.19).
DoD: only green on real assertions.

### IMPL-802 Security sign-off (rules/CF/AT-1..20)
Objective: rerun emulator Security-Rules/CF tests; confirm every AT-1..20 control holds; hostile review (FINAL-READINESS Reviewer B) sign-off.
Source: `SECURITY-ATTACK-REVIEW.md`, `FINAL-FIREBASE-SECURITY-MODEL.md`, `AUDIT-LOG-INTEGRITY.md`.
Acceptance: escalation/ownership/forge/deep-link/storage/timestamp/duplicate tests all pass.

### IMPL-803 Performance macrobenchmark run
Objective: measure startup/scroll/frame/query on a reference device; confirm FINAL-PERFORMANCE targets or record how measured.
Source: `FINAL-PERFORMANCE-CONTRACT.md`.
Tests: FT-PERF. Acceptance: no fabricated numbers; baseline profile committed.

### IMPL-804 Release gate + SEC-1 remediation + signing + rollout
Objective: SEC-1 keystore remediation (rotate+purge); Play App Signing key; release build on clean machine; PRE-RELEASE gate; staged rollout; Crashlytics/monitoring live.
Source: `FINAL-DECISION-REGISTER.md` D-4/SEC-1; `PRE-RELEASE.md`; `CI-CD-ARCHITECTURE.md`; AGENT §7.
Blockers: D-4 identity + signing key + env (BLOCKED until owner + env provided).
Acceptance: clean release; no committed secrets; store-upload evidence.

### IMPL-805 Programme close-out audit
Objective: IMPLEMENTATION CONTROL final audit (§27 of the brief) — coverage/completeness/consistency/agent-safety; update PROGRESS-TRACKER, TRACEABILITY, COMPLETION-REGISTER; produce the §28 report.
Source: all. Acceptance: orphan requirements 0, orphan tasks 0, no unexplained gaps.
