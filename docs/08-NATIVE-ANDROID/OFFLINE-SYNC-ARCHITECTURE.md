# Offline / Synchronization Architecture

Status: PROPOSED. Explicit decision on source-of-truth per domain. The Flutter app was offline-first/Hive — we do **not** blindly carry that; we decide per domain.

## 1. Decision framework per domain
| Domain | Source of truth | Offline read | Offline write | Sync | Conflict | Last-write | Retry/Queue | Recovery |
|---|---|---|---|---|---|---|---|---|
| Users/roles/org | Cloud (Firestore + CF) | cached read only | NO (role/profile mgmt requires server) | stream | server-authoritative | server | no local queue | re-auth/fetch |
| Drivers/Labourers catalogue | Cloud | cached read only | NO (owner writes online or queued) | stream | server-authoritative (CF) | server | optional queue | refetch |
| WorkSession/Trips | Cloud (authoritative) + local cache | YES (cache) | YES offline queue for owner | batch + stream | versioned; no silent overwrite | merge w/ last-write-wins only where safe | WorkManager queue | reconcile on reconnect |
| Attendance | Cloud + audit | YES (cache) | YES owner records offline; worker confirm via CF | queue | preserve both sides; explicit resolution | server validates | queue | reconcile; audit |
| Notifications | Cloud | cached history | n/a | stream | n/a | server | retry send | refetch |
| Drafts | Local (Room/DataStore) | YES | YES (local) | none | n/a | local | n/a | autosave |
| Settings | Cloud + local cache | cached | owner online | stream | server | server | no | refetch |

## 2. Recommended model: **Cloud-authoritative with offline-capable owner data entry**
- Firestore enabled offline persistence gives cache reads + pending writes.
- Owner is the primary writer; their field entries can be made offline-safe via an explicit offline queue in Room for reliability (rather than relying solely on Firestore pending writes), then replayed by WorkManager.
- Drivers/labourers (if self-service) read own data; any confirmation write is small and can be queued.
- Non-writers (view-only roles) get cached reads + a clear "offline" indicator; no silent success.

## 3. Offline-first vs Cloud-first conclusion
- **Not offline-authoritative.** Use **hybrid cached state**: Firestore is authoritative for shared/business data; local cache improves offline read and provides a durable offline write queue for owner data entry. Data integrity (trip numbering) must reconcile server-side, not locally.

## 4. Sync model
```
Local mutation (owner/driver) → pending operation (Room outbox)
   → network available → sync (WorkManager)
        → server validation (CF / rules)
             → Success | Conflict | Rejection
                  → local reconciliation (apply server result; surface conflict)
```
- Conflict behavior: use `updatedAt` + revision/version; on divergence, do **not** silently overwrite. Present resolution (keep mine / keep server) for owner-editable records; automated last-write-wins only where safe & documented (e.g., attendance toggle latest by server time + audit).
- Outbox must be transactional with local cache (write local + enqueue in one DB transaction).

## 5. Retry/backoff & idempotency
- WorkManager with backoff; each queued op has an idempotency key; server rejects duplicate/out-of-order ops (see BACKEND-CONTRACT).

## 6. Failure & expiry during offline
- Offline sign-in: allowed only if a valid session token exists and rules permit cached auth; otherwise require connectivity. Sessions revoked → Forbidden, reconcile keeps user's unsent outbox clearly labelled (never discard).

## 7. Data-loss guardrails
- Never drop the outbox on sign-out/account switch without explicit user consent.
- No fake success: if a write is only queued (offline), UI shows "pending/syncing", not "saved to server".

## 8. Emulator for dev
All sync/conflict logic validated against the Firebase Emulator (never production) before release. See `ANDROID-TESTING-ARCHITECTURE.md`.

## 9. Verification
PROPOSED. Ties to ADR-012. Needs product confirmation on conflict UX (D-6/D-1).
