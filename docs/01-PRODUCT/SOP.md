# Standard Operating Procedures (product/engineering)

Purpose: Repeatable procedures for the team operating on this codebase and future native work. Status: VERIFIED steps are code-backed; others are PROPOSED operating conventions.

Commit `2dd2fe4`. Audit date 2026-09-07.

## 1. Definition of done for a change (current Flutter repo)
1. Source change compiles under the repo's Dart 3.11+/Flutter setup (`flutter analyze` clean).
2. Affected BLoC unit tests pass (`flutter test test/unit/...`).
3. Widget tests for changed screens pass (`flutter test test/widget/...`).
4. Data-integrity integration tests pass (restore rollback, date partition, trip numbering).
5. No TODO/FIXME/dummy/placeholder added.
6. No secrets added; no key/credential file committed.
7. Manual smoke of the affected journey on an emulator (see `docs/screenshots` gallery for expected states).

> `UNVERIFIED (env)`: this audit environment had no Flutter SDK, so `flutter analyze/test/build` could not be re-run here. Steps 1–7 are the repository's documented standard, cross-checked against the existing docs + tests.

## 2. Backup / restore SOP (VERIFIED — product-level)
- **Back up weekly** or before any high-risk change: Settings → Backup Database → save `.labourbackup` somewhere safe.
- **Migrate device**: export `.labourbackup`, install app on new phone, Restore.
- **Test restores**: restore into a scratch device before discarding an old backup.
- Keep backups unencrypted-file risk in mind (PII: names/phones) — store securely. (See `04-SECURITY/`.)

## 3. Trip/day close-out SOP (VERIFIED flows)
- At day end, verify Dashboard totals equal paper/memory; reconcile by opening History for the date.
- Ensure attendance toggles are Present/Absent correctly before day ends.
- Optionally export backup as a permanent ledger snapshot.

## 4. Native Android engineering SOP (PROPOSED)
1. Change flows through Clean Architecture layers (UI → ViewModel → UseCase → Repository → Firebase/local).
2. Authorization is enforced in backend security rules + Cloud Functions; UI role flags never authorize.
3. State via sealed UI state (Loading/Success/Empty/Error/Offline/Unauthorized/Forbidden) in `StateFlow`.
4. Never log secrets; never commit credentials; App Check enabled.
5. Every privileged path has an audit log entry.
6. Release gate per `06-QUALITY/PRE-RELEASE.md`.

## 5. Incident response SOP
See `07-OPERATIONS/INCIDENT-RESPONSE.md` (currently documentation-only; native service expected later).

## Verification status
VERIFIED: sections 1 (as repo standard), 2, 3. PROPOSED: section 4, 5, and re-execution of the local SDK steps (`UNVERIFIED`).
