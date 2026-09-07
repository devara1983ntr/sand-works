# Phase 2 — Data & Offline Layer — Task Contracts

Phase objective: Room cache+outbox, repository contracts & local-first impl, sync/WorkManager with idempotency, optimistic concurrency & conflict scaffolding. Prerequisites: Phase 1 (Gate-1). Execution is BLOCKED until Gate-0 (READY) because it builds on the identity/environment-gated skeleton.

Shared refs: `FINAL-DATABASE-SCHEMA.md`, `FINAL-QUERY-RULE-MATRIX.md`, `FINAL-OFFLINE-SYNC.md`, `CONCURRENCY-SPECIFICATION.md`, `DATABASE-COMPLETENESS.md`, AGENT §14.

---

### IMPL-201 Room DAOs, migrations, indexes, queries
Title: DAO implementations + migrations + local query set
Status: READY (execution BLOCKED by Gate-1/Gate-0). Priority P0. Type: data.
Objective: Implement DAOs for every local entity, schema migrations, and the local query set supporting screens (today's sessions, trips by session, labour/driver lists, attendance by trip, outbox queries).
Why: offline cache must serve all owner reads offline per OFFLINE-STATE-MATRIX.
Source: `FINAL-DATABASE-SCHEMA.md` Local store + collections; `FINAL-QUERY-RULE-MATRIX.md` Q-* (local subset); screen data needs.
Expected files: DAO impls, migration objects, Room indices.
Expected implementation: real SQL; indexes on queried columns; no full-table scans for hot paths (PERF).
Business rules: R-01 (uniqueness guard in local cache). Conditional: n/a.
Security: local data protected (encrypted cache per security architecture); no secrets.
Data: exact fields from FINAL-DATABASE-SCHEMA. Error: typed. Loading/Empty/Offline: n/a (data layer). Accessibility: n/a. Performance: indexes + pagination.
Tests: DAO/repository data-layer tests; migration tests (1→latest).
Acceptance: offline reads serve all V1 owner queries; migrations lossless.
DoD: template. Risks: schema drift. Blockers: Gate-0.
Out-of-scope: Firestore sync (Phase 3). Downstream: IMPL-202..204, Phase 4.

### IMPL-202 Repository contracts + local-first implementations
Title: Repository interface layer + Room-backed impls (cache-first)
Status: READY (execution BLOCKED). Priority P0. Type: data.
Objective: Repository interfaces per aggregate; local-first implementations returning Room data + exposing write ops that queue to outbox when offline.
Why: presentation talks to repository, never Room directly (AGENT §3); supports offline-first.
Source: `ANDROID-ARCHITECTURE.md`, `FINAL-OFFLINE-SYNC.md`, `FINAL-ACTION-REACTION-MATRIX.md`.
Expected files: repository interfaces, impls, mappers.
Business rules: R-*. Conditional: connectivity branch (C-N*). Security: repository never performs privileged op client-side; forwards to CF in Phase 3.
Data: entities. Error: typed. Offline: reads cache + stale tag; writes → outbox (no fake success).
Tests: repository tests (local). Acceptance: all owner read paths served locally.
DoD: template. Downstream: Phase 4 VMs.

### IMPL-203 Outbox + WorkManager sync engine + idempotency
Title: Deterministic outbox replay + sync worker + backoff
Status: READY (execution BLOCKED). Priority P0. Type: data.
Objective: WorkManager sync that replays queued ops in dependency order with idempotency (opId) and exponential backoff; no fire-and-forget.
Why: durable offline sync; no duplicate/lost ops; no fake success (AGENT §14.4/14.6).
Source: `CONCURRENCY-SPECIFICATION.md`, `FINAL-OFFLINE-SYNC.md`.
Expected files: outbox processor, sync worker, connectivity monitor, sync-state emitter.
Security: ops map to server ops with opId; server re-validates (Phase 3).
Offline: pending count exposed; only server ack clears. Error: sync-failed state + retry.
Tests: sync order/idempotency/backoff tests (FT-OFFLINE/FT-CONC).
Acceptance: deterministic replay; double-replay deduped.
DoD: template. Downstream: Phase 4 sync indicators.

### IMPL-204 Optimistic concurrency + conflict scaffolding
Title: `rev` handling + conflict detection/resolution model
Status: READY (execution BLOCKED). Priority P0. Type: data.
Objective: Read/store `rev` on editable docs; detect mismatch; provide conflict resolution primitives (keep/reload/merge) surfaced to UI; never silent overwrite.
Why: multi-device owner edits / offline vs online divergence (EDGE E9/E16/E25).
Source: `CONCURRENCY-SPECIFICATION.md`, `FINAL-SCREEN-STATE-MATRIX.md` (CONFLICT), `EDGE-CASE-AUDIT.md`.
Expected files: revision store, conflict type, resolver.
Business rules: R (rev). Security: rev is server-validated (Phase 3). Error: Conflict typed.
Tests: conflict detection/resolution tests. Acceptance: no silent last-write-wins.
DoD: template. Downstream: Phase 4 conflict UI (N-23/24/25).
