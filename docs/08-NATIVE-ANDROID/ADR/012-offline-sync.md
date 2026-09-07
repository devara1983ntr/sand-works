# ADR-012: Offline / sync strategy

## Context
The reference was offline-first/Hive. We must decide source-of-truth per domain and not blindly carry offline-first forward.

## Decision
Use a **Cloud-authoritative with offline-capable owner data entry** model:
- Firestore offline persistence for cached reads + pending writes.
- A durable **Room outbox** for owner data entry (WorkSession/Trips/Attendance) so offline writes are reliable and reconcile via WorkManager — not relying solely on Firestore pending-writes.
- View-only roles (driver/labourer reads) use cache with a clear offline indicator; no fake success.
- Conflict: never silently overwrite; version/`updatedAt`-based; present resolution for owner-editable records; server-validated last-write-wins only where safe & audited.
- Draft autosave is local.

Per-domain decision table in OFFLINE-SYNC-ARCHITECTURE.md.

## Why chosen
- Field usage (owner on site) needs reliable offline entry → hybrid cached.
- Shared/multi-role data and security need cloud authority.
- Audit + no-silent-overwrite satisfies data-integrity expectations.

## Alternatives
- Pure offline-authoritative (Flutter-style): no secure multi-user, no shared truth.
- Pure cloud-only: poor field UX on connectivity loss.

## Trade-offs / consequences
- Sync/conflict/reconciliation complexity (must be tested with Emulator).
- Outbox must be transactional with local cache; idempotency keys.
- Non-writers get read-only cache semantics.
