# Phase 1 — Foundations — Task Contracts

Phase objective: project skeleton, build/DI/nav/theme/logging, core domain + invariants, local persistence foundation. Excluded: any Firebase/network (Phase 3), any feature UI logic beyond scaffolds. Prerequisites: **Gate-0 (IMPLEMENTATION STATUS READY)** — these tasks are BLOCKED until owner confirms scope S-V1 + D-4 identity + environment; they are written as contracts now and executed later.

Shared references: DESIGN = `08-NATIVE-ANDROID/ANDROID-DESIGN-SYSTEM.md`, `ANDROID-ARCHITECTURE.md`, `DOMAIN-MODEL.md`, `ADR/`; RULES = `03-ENGINEERING/AGENT.md §14`; DEP = `08-NATIVE-ANDROID/DEPENDENCY-POLICY.md`.

---

### IMPL-101 Project skeleton & Gradle
Title: Scaffold Gradle project, variants, applicationId
Status: BLOCKED (Gate-0: D-4 package/applicationId/signing identity; env).
Priority: P0. Type: foundation.
Objective: Create the Kotlin/Compose Gradle project with build variants and correct applicationId/namespace.
Why: App must exist as a buildable unit with stable identity (update-path continuity per D-4).
Source: `FINAL-DECISION-REGISTER.md` D-4; `08-NATIVE-ANDROID/ANDROID-ARCHITECTURE.md`; `DEPENDENCY-POLICY.md`.
Prerequisites: Gate-0. Dependencies: none upstream. Inputs: D-4 answer, DEP catalog.
Expected files: `settings.gradle.kts`, root+app `build.gradle.kts`, `gradle/libs.versions.toml` (from DEP), variant config (debug/release), minSdk 24, compileSdk per DEP re-verified, `AndroidManifest.xml`.
Expected implementation: Real build; no dummy modules.
Business rules: n/a. Conditional logic: variant/debug-build wiring only.
Security: no secrets in Gradle; signing config references env/key.properties per SEC-1 remediation, never committed.
Data/UI/UX/Error/Loading/Empty/Offline/Accessibility/Performance: n/a at this task (foundation).
Tests: build + `:app` assembleDebug on clean machine.
Acceptance: `./gradlew assembleDebug` green; identity correct.
DoD: per template.
Risks: version matrix must be re-verified (DEP) on Flutter-independent machine. Blockers: D-4 unresolved (BLOCKED — BUSINESS DECISION); env absent.
Out-of-scope: Firebase wiring, feature code.
Evidence: build log, toml. Downstream: IMPL-103..106, 108.

### IMPL-102 Dependency & version catalog
Title: Apply dependency policy catalog
Status: READY to author (blocked only by D-4/environment for execution).
Priority: P0. Type: foundation.
Objective: Lock a justified, re-verified dependency set (Compose BOM, M3, Navigation, Hilt, Room, DataStore, WorkManager, Coil, coroutines, Firebase libs) with no undocumented additions.
Why: DEP policy + "no undocumented dependencies" (AGENT §14).
Source: `DEPENDENCY-POLICY.md`; AGENT §14.
Prerequisites: none (document). Dependencies: re-verify versions on a Flutter-independent machine (environment blocker).
Expected files: `gradle/libs.versions.toml` + dependency notes.
Expected implementation: justify each dep; no deps beyond approved set.
Tests: dependency/license scan; build (later).
Acceptance: catalog matches DEP matrix; nothing added without reason.
DoD: template. Blockers: version re-verification env (BLOCKED — ENVIRONMENT).
Downstream: IMPL-101.

### IMPL-103 Application config & secrets handling
Title: Config, build-type applicationId, safe secrets handling
Status: READY (execution BLOCKED by Gate-0/env). Priority P1. Type: foundation.
Objective: Centralised config (API/project refs) with secrets never committed (AGENT §7, SEC-1).
Why: SEC-1 + no secrets in source.
Expected files: config classes, google-services.json reference (NOT committed — env), key handling.
Security: keystore/key.properties excluded; google-services from env; SEC-1 remediation applied.
DoD: secret scan clean. Blockers: google-services/keys = BLOCKED — CREDENTIAL/ENVIRONMENT.
Downstream: IMPL-301 (Firebase).

### IMPL-104 Hilt DI foundation
Title: Dependency injection container
Status: READY (execution BLOCKED by Gate-0). Priority P0. Type: foundation.
Objective: Hilt modules wiring app/domain/data scopes.
Why: architecture (ADR-006); enables testability & clean layering.
Expected files: `Application`, modules, qualifiers. Tests: DI graph unit test.
Acceptance: graph builds; no production fakes. Downstream: all.

### IMPL-105 Logging & error infrastructure
Title: Typed logging + error handling client
Status: READY (execution BLOCKED). Priority P0. Type: foundation.
Objective: App-scoped logger + typed `Error` → state mapping (FINAL-ERROR-CONTRACT), no silent catch (AGENT §14.16).
Why: observable, honest failures.
Expected files: `Logger`, sealed `AppError`, error→message mapper; analytics hook (no PII).
Security: no PII/secrets in logs.
Tests: error mapping unit tests. Downstream: all UI error states.

### IMPL-106 Navigation foundation
Title: Auth + owner-shell navigation scaffolds + Back contract
Status: READY (execution BLOCKED by Gate-0). Priority P0. Type: foundation.
Objective: Navigation Compose graph structure (auth/main/detail) with Back contract per FINAL-NAVIGATION.
Why: no orphan route/dead-end; back defined.
Source: `FINAL-NAVIGATION.md`; `FINAL-SCREEN-CATALOG.md` SSC.
Expected files: nav graph, destinations, back/dirty handling, deep-link stubs (wired later).
Tests: navigation + back tests (FT-NAV). Downstream: Phase 4 screens.

### IMPL-107 Design system & theme
Title: M3 design system tokens + light/dark/dynamic theme
Status: READY (execution BLOCKED by Gate-0/D-4 brand). Priority P0. Type: foundation.
Objective: Typography/spacing/shapes/colors + M3 components + theme (light/dark/dynamic) per ANDROID-DESIGN-SYSTEM.
Why: cross-screen consistency; fixes dark-only (NFR-02 defect).
Source: `ANDROID-DESIGN-SYSTEM.md`, `FINAL-RESPONSIVE.md`, `FINAL-ACCESSIBILITY.md`.
Expected files: `ui/theme/*`, component library.
Accessibility: contrast AA, dynamic type, touch ≥48dp baked into components.
Blockers: brand/logo asset (D-4) — logo handled separately (IMPL-704). DoD: template. Downstream: all UI.

### IMPL-108 Core domain model & mappers
Title: Domain entities, value objects, DTO mappers
Status: READY (execution BLOCKED by Gate-0). Priority P0. Type: domain.
Objective: Implement entities/values/DTOs/mappers for Work/session/trip/labour/attendance/draft + org/user (mirror DOMAIN-MODEL, FINAL-DATABASE-SCHEMA). Domain independent of Android/Firebase (AGENT §3).
Source: `DOMAIN-MODEL.md`, `FINAL-DATABASE-SCHEMA.md`, `FINAL-WORK-STATE-MACHINE.md`.
Expected files: domain entities, value objects (e.g., SessionBoundary per D-5), mappers.
Business rules: R-01..R-40 structural (fields present). Validation: field types/enums.
Data: field set per FINAL-DATABASE-SCHEMA. Tests: domain unit tests.
DoD: template. Downstream: IMPL-109, Phase 2.

### IMPL-109 Domain invariants & use cases (business rules)
Title: Use cases + invariant enforcement (R-rules) + unit tests
Status: READY (execution BLOCKED by Gate-0). Priority P0. Type: domain.
Objective: Implement business-rule use cases (session uniqueness, trip numbering policy hooks, attendance corrections, delete cascade policy, close) as domain logic with unit tests; server enforcement still required (Phase 3).
Source: `FINAL-BUSINESS-RULES.md` (R-01..R-90), `FINAL-CONDITIONAL-LOGIC.md`, `FINAL-WORK-STATE-MACHINE.md`.
Expected files: use cases, result/error types.
Business rules: all R-*. Conditional: C-* branches. Validation.
Tests: unit tests per R-rule (FT mapping). Acceptance: every R-rule exercised in domain.
Blockers: number-authority is server-side (Phase 3) — domain defines policy; actual authority in CF.
Downstream: Phase 2, Phase 4.

### IMPL-110 Local persistence schema foundation
Title: Room + DataStore + outbox schema definition
Status: READY (execution BLOCKED by Gate-0). Priority P0. Type: data.
Objective: Declare Room entities (cache mirrors of Firestore records) + DataStore prefs/draft + outbox table (opId, type, payload, depGroup, state) per FINAL-DATABASE-SCHEMA local section.
Why: offline-first cache + deterministic outbox.
Source: `FINAL-DATABASE-SCHEMA.md` (Local store), `FINAL-OFFLINE-SYNC.md`, `CONCURRENCY-SPECIFICATION.md`.
Expected files: Room `@Entity`/`@Database`, DAO interfaces (impl Phase 2), outbox entity, DataStore.
Tests: schema/type unit tests. DoD: template. Downstream: Phase 2.
