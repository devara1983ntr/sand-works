# Offline State Matrix

Status: Phase 0.5 (PROPOSED). Extends OFFLINE-SYNC-ARCHITECTURE. Define connectivity states and per-feature offline capability.

## Connectivity states (UI banner/indicator)
| State | Indicator | Meaning |
|---|---|---|
| Online | none | synced; live |
| Offline | banner "Offline" | no network; reads from cache; writes queue |
| Stale | subtle "Updated X ago" | showing cached data older than threshold |
| Syncing | "Syncing…" + pending count | outbox flushing |
| Sync failed | "Sync failed — Retry" | network or server error flushing queue |
| Conflict | conflict dialog/banner | a queued/offline write diverged; needs resolution |
| Recovered | "Changes synced" | queue drained successfully |

## Feature offline classification
| Feature | Offline read | Offline write | Action | Blocked | Queued | Notes |
|---|---|---|---|---|---|---|
| WorkSession/Trip create/edit (owner) | YES (cache) | YES | field entry | — | queue (Room outbox) | reconcile on reconnect |
| Attendance record (owner) | YES | YES | record | — | queue | idempotent; corrections queued |
| Attendance correction after confirm | read yes | NO (needs server + reason) | — | YES online | — | audit requirement |
| Delete trip/session | read yes | YES (queue) | soft-delete | — | queue via CF | must revalidate online |
| Role/user mgmt | read (cache) | NO | — | YES online | NO (server-authoritative) | owner CF only |
| Crew catalogue | read (cache) | queue optional | add/edit | — | queue (owner) | reconcile |
| Login | blocked offline (unless cached-session auth allowed; decision) | — | — | YES | NO | token expiry policy |
| Notifications read | read cached | mark-read queue (decide) | — | — | optional | |
| Send announcement | read | NO | — | YES online | NO (CF) | owner |
| Reports | read cached | n/a | — | n/a | refetch | aggregation may need online |
| Backup local export | YES (local file) | — | export | — | — | offline ok |
| Cloud backup / restore | — | NO | — | YES online | NO (CF) | |
| Profile edit | cache | queue | — | — | queue | self limited fields |
| Search (history) | YES (cached index) | n/a | — | n/a | — | scope to cache |

## Rules
- **No fake success**: an offline "save" must clearly show queued/pending-sync, never claim server-saved.
- Outbox never dropped on sign-out/account switch without explicit user consent; reconcile after re-auth.
- Blocked operations show reason (online required) not a generic error.
- Conflict handling: never silently overwrite (offline/online divergence) — present resolution for owner-editable; server-validated last-write only where safe+audited (see DATA-CONSISTENCY, CONCURRENCY).

## Gaps flagged
- Login-offline policy (allow cached-session auth?) → REQUIRES DECISION.
- Offline queue visibility/management UI (pending count, per-item retry, discard) → define (PROPOSED).
- Mark-notification-read offline semantics → decide.
- Verification PROPOSED; offline states per feature tested (ROOM outbox + WorkManager + emulator).
