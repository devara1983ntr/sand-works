# FINAL CONDITIONAL LOGIC SPECIFICATION — V1 (Phase 0.75)

For every important condition: Why · True branch · False branch · UI effect · Data effect · Backend effect · Authorization · Navigation · Notification · Recovery. **No conditional may have an undefined false path.** Scope S-V1 (single owner). Reference: Phase-0.5 CONDITIONAL-LOGIC-MATRIX reconciled to S-V1.

Global principle: UI derives from an immutable UiState driven by (auth, role, connectivity, data, form, sync) — never from ad-hoc checks. Each branch is a tested state transition.

## Authentication / session
### C-A1 Signed in (session valid)?
Why: gate. TRUE → resolve role → N-10 (owner). FALSE → N-03 login (preserve deep link). UI: login. Data: none. Backend: Auth state listener. Recovery: retry.
### C-A2 Session expired / token refresh failed?
TRUE → N-08 re-auth; preserve drafts/outbox; FALSE → continue. After re-auth reconcile outbox. Notification: security alert optional.
### C-A3 Account disabled?
TRUE → N-06 notice; no data; FALSE → normal. Backend: server status check each op (Firestore rule denies disabled users via claim/status).
### C-A4 Role == OWNER?
TRUE → owner shell + owner actions; FALSE → Forbidden (V1 single owner → practically only if provisioning is wrong). Backend rule denies. UI Forbidden distinct.
### C-A5 First run (no account provisioned)?
TRUE → N-07 provisioning (CF); FALSE → N-03.

## Network / sync
### C-N1 Online?
TRUE → live read/write allowed; FALSE → offline banner + cache + outbox queue; offline reads from Room cache; offline writes queue with pending label.
### C-N2 Pending queued operations exist on reconnect?
TRUE → auto-sync (idempotent replay) → SUCCESS or CONFLICT/PARTIAL; FALSE → normal.
### C-N3 Sync succeeded?
TRUE → clear pending, update indicator "changes synced"; FALSE → Sync-failed state [Retry], keep queue.
### C-N4 Conflict detected on sync/save?
TRUE → present conflict resolution (keep/merge/reload) for owner-editable; server-derived values resolved via CF; FALSE → proceed.
### C-N5 Report/export offline and no cache/derived data ready?
TRUE → block with reason; FALSE → export.

## Data existence
### C-D1 Query returns data?
TRUE → SUCCESS list; FALSE → EMPTY state with next action. Reactive: adding first record flips EMPTY→SUCCESS.
### C-D2 Labour/driver/vehicle catalogue empty?
TRUE → prompt to add catalogue before full trip entry; FALSE → pickers enabled.
### C-D3 Session already open for (date,session)?
TRUE → offer open/edit existing; block duplicate create (server uniqueness); FALSE → create new.

## Work/trip status
### C-W1 Work session open vs closed?
Open → can add trips/edit (within rules); Closed → immutable-ish; owner correction allowed only in restricted window + reason + audit (D-8). Reactive change while viewing → show state.
### C-W2 Editing a completed/confirmed attendance record?
TRUE → require correction reason + audit; FALSE → normal record.

## Assignment (owner-driven; no driver app in V1)
### C-W3 Trip has driver assigned (record)?
TRUE → show driver/vehicle; FALSE → assignable (owner). No driver acceptance state in V1 (D-6 deferred).

## Permission (Android)
### C-P1 Notifications permission (POST_NOTIFICATIONS)?
Relevant only if V1-OPTIONAL N-41 notifications shipped. TRUE → channel enabled; FALSE → rationale + settings; notification centre still works (read in-app). No broad request.
### C-P2 Storage/photo permission for avatar/CSV?
Use SAF (Storage Access Framework) → no runtime storage permission needed for export/import; avatar via photo picker (no permission). FALSE path handled by system picker UI.

## Form / draft
### C-F1 Form dirty with unsaved changes on exit?
TRUE (new) → autosave draft (reference behaviour) OR explicit discard-confirm (decision per form: default autosave); TRUE (saved once) → keep; FALSE → allow nav.
### C-F2 Validation fails on submit?
TRUE → inline errors + block submit (client) and server re-validates; FALSE → submit.
### C-F3 Session expired while form dirty?
TRUE → autosave draft/outbox then re-auth (preserve); FALSE → normal.

## Sync state
### C-S1 A given record is local-pending / syncing / synced / conflicted?
Drives per-item indicator (pending/queued, syncing, synced, conflict). Not a binary; a small state machine on each queued op.

## Notifications (V1-OPTIONAL owner system events)
### C-NT1 Backup complete/fail?
Complete → success notification (+ deep link N-38); Fail → failure notification with retry. Read state update offline decision.

## Recovery matrix
| Condition | Recovery |
|---|---|
| Session expired | N-08 re-auth, preserve outbox/drafts |
| Offline write | queue + retry on reconnect (idempotent) |
| Conflict | owner prompt (keep/merge/reload) |
| Forbidden | message + route to allowed area |
| Disabled | N-06 notice |

## Verification
Every condition has both branches; each maps to a UiState and a test. Undefined-false-path conditions introduced during implementation must be flagged (IMPLEMENTATION-CONTRACT).
