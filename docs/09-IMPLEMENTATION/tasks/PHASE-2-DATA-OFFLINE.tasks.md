# Phase 2 — Data & Offline Layer — Task Contracts

Phase objective: local cache + deterministic outbox, repository contracts, offline/sync with idempotency and conflict. Prereq Phase 1 (Gate-1). Execution gated by Gate-0.
Shared refs: `10-SANDWORKS/DATA-MODEL.md`, `OFFLINE-SYNC-SPEC.md`, `CONCURRENCY-IDEMPOTENCY-AUDIT.md`, AGENT §14.

### SW-201 Room schema, migrations, DAOs, local query set
Title: Room entities (mirror cache), outbox table, DataStore, migrations, DAOs, indexes.
Status: READY (gated). Priority P0. Type: data.
Objective: offline cache + outbox (`opId,type,payload,depGroup,state`) + prefs supporting owner/driver/labourer offline reads + queued writes.
Why: offline-first (§25); no fake success; deterministic replay.
Source: `DATA-MODEL.md` (local store), `OFFLINE-SYNC-SPEC.md`, `CONCURRENCY-IDEMPOTENCY-AUDIT.md`.
Security: encrypted local cache; no secrets. Tests: DAO/migration. Acceptance: all owner/driver/labourer read paths served offline.
Blockers: Gate-0. Downstream: SW-202..204, phases 5-7.

### SW-202 Repository contracts + local-first implementations
Title: Repository interfaces per aggregate + Room-backed cache-first impls; writes queue when offline.
Source: `DATA-MODEL.md`, `OFFLINE-SYNC-SPEC.md`. Tests: repository (local). Acceptance: reads local-first; writes route to outbox offline.
Downstream: SW-203, VMs in 5-7.

### SW-203 Outbox + WorkManager sync + idempotency + backoff
Title: Deterministic replay (dependency order) + sync worker + opId idempotency + backoff; never fire-and-forget.
Source: `OFFLINE-SYNC-SPEC.md`, `CONCURRENCY-IDEMPOTENCY-AUDIT.md`. Security: maps to server ops with opId; server re-validates (P3). Offline: pending exposed; no fake success (AGENT §14.6). Tests: sync order/idempotency/backoff (FT-OFFLINE/FT-CONC).
Acceptance: replay deterministic; double-replay deduped; closure never double. Downstream: phases 5-7 sync indicators.

### SW-204 Optimistic concurrency (`rev`) + conflict primitives
Title: revision store, conflict detection, resolution model (keep/reload/merge); never silent last-write-wins.
Source: `CONCURRENCY-IDEMPOTENCY-AUDIT.md`, `SCREEN-STATE-CONTRACT.md`. Tests: conflict tests. Downstream: edit screens (trip/rate/assignment/settings).
