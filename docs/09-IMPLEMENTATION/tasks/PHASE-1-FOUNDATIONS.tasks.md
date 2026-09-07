# Phase 1 — Foundations & Identity — Task Contracts

Phase objective: create the SAND WORKS project skeleton (package `com.roshan.sandworks`), DI/nav foundation, design system/theme, error infra, and the domain/money core. **No Firebase in this phase** (Phase 3). No release signing here (SW-BLK-3 gates the release build in SW-904, not DEBUG scaffold).
Shared refs: `10-SANDWORKS/DIRECTIVE-REGISTER.md`, `ASSET-INVENTORY.md`, `UX-DESIGN-SYSTEM.md`, `SCREEN-STATE-CONTRACT.md`, `MONEY-ENGINE-SPEC.md`; `08-NATIVE-ANDROID/ADR/` + `DEPENDENCY-POLICY.md` (non-conflicting). All tasks honour AGENT §14 (no placeholders/fabrication). Each task status below is `READY(scope)` = Gate-0 cleared, buildable; flip to IN-PROGRESS/DONE on execution.

### SW-101 Project skeleton & Gradle (SAND WORKS)
Title: Scaffold Gradle, variants, applicationId `com.roshan.sandworks`.
Status: READY (scope; Gate-0 clear). Priority P0. Type: foundation.
Objective: buildable Kotlin/Compose/M3 Gradle project with correct package; debug (default) buildable without signing.
Why: locked identity `com.roshan.sandworks` (directive §42); base for all downstream.
Source: `DIRECTIVE-REGISTER.md` (SW/D-4), `ASSET-INVENTORY.md`, `DEPENDENCY-POLICY.md`.
Expected files: `settings.gradle.kts`, root+app `build.gradle.kts`, `gradle/libs.versions.toml`, `AndroidManifest.xml`, debug/release config.
Security: no secrets in Gradle; signing via env/ignored file only (release); minSdk per DEP re-verified; never `com.roshan.labourparty`.
Tests: `assembleDebug` on clean machine. Acceptance: DEBUG builds; package correct. Release-signing (SW-BLK-3) deferred to SW-904.
Downstream: SW-102..108.

### SW-102 Dependency & version catalog
Title: Lock justified dependency set (M3, Navigation, Hilt, Room, DataStore, WorkManager, Coil, coroutines; Firebase libs deferred to SW-301).
Status: READY (scope). Priority P0. Type: foundation.
Source: `DEPENDENCY-POLICY.md`; AGENT §14. Expected: `libs.versions.toml` + per-dep justification (no undocumented deps).
Tests: license/build scan. Acceptance: versions pinned and justified. Downstream: all.

### SW-103 App config & secrets handling
Title: Config + safe secrets (google-services from env/ignored file; never committed).
Status: READY (scope). Priority P1. Type: foundation.
Objective: secrets/config scaffold such that a real `google-services.json` can later be dropped in via env without source change (Phase 3 consumes it).
Security: `key.properties` excluded; no secrets in source (AGENT §7). Real Firebase wiring is SW-301, not here.
Downstream: SW-301.

### SW-104 Hilt DI foundation
Title: Dependency injection graph (app/domain/data modules).
Status: READY (scope). Priority P0. Type: foundation.
Source: `08-NATIVE-ANDROID/ADR-006`, `10-SANDWORKS` layering. Expected: `Application`, modules, qualifiers.
Tests: DI graph unit. Downstream: all.

### SW-105 Logging & typed error infrastructure
Title: Logger + sealed typed errors → message mapping (no silent catch).
Status: READY (scope). Priority P0. Type: foundation.
Source: `10-SANDWORKS/SCREEN-STATE-CONTRACT.md`; AGENT §14.16. Tests: error mapping. Downstream: all screens.

### SW-106 Navigation foundation & deep-link model
Title: Compose nav graph + route register + deep-link contract (auth re-validation handled in P3).
Status: READY (scope). Priority P0. Type: foundation.
Source: `10-SANDWORKS/NAVIGATION.md`, `08-NATIVE-ANDROID/FINAL-NAVIGATION.md`. Tests: nav/back. Downstream: P5-7 shells.

### SW-107 Money & time primitives
Title: Integer-rupee (paise) type, rounding policy, ₹ formatting; IST date boundaries (UTC stored, IST day boundary) — pure, no Firebase.
Status: READY (scope). Priority P0. Type: domain.
Source: `10-SANDWORKS/MONEY-ENGINE-SPEC.md`, `SCHEDULING-SPEC.md`. Tests: integer math, day-boundary. Downstream: SW-108, P4.

### SW-108 Money engine domain logic (pure, testable)
Title: deterministic equal/custom distribution, rate-snapshot immutability, per-user accrued accrual, daily/periodic aggregation math (surfaces only in P4/P7).
Status: READY (scope). Priority P0. Type: domain.
Source: `MONEY-ENGINE-SPEC.md`; `CONCURRENCY-IDEMPOTENCY-AUDIT.md` (math idempotency). Security: pure function; server/CF re-validates results (P3). Tests: distribution, rounding, snapshot. Acceptance: results match MONEY-ENGINE-SPEC worked examples.
Downstream: SW-401..404.
