# FINAL DECISION REGISTER (Phase 0.75)

Status: Phase 0.75 closure gate. This register resolves every open decision from Phases 0/0.5 **to the extent evidence permits**. Where genuine business/owner input is required and is NOT present in any authoritative source, the decision is marked **BLOCKING BUSINESS DECISION** with a recommended default so that a coherent implementation contract exists. Phase 0.75 does not fabricate business answers.

Source-of-truth used (in hierarchy order): repository code (verified single-operator offline product) > runtime evidence (UNVERIFIED, no SDK) > tests > PRD/PRD2 > Phase 0 docs/ADR > Phase 0.5 docs > decisions in this register > recommendations.

Legend: RESOLVED (has source basis) · RECOMMENDED-DEFAULT (strong technical basis, awaiting owner sign-off) · BLOCKING BUSINESS DECISION (requires owner confirmation before a different scope is permitted; default supplied).

---

## Scope context used by every document (S-V1)
Recommended V1 scope (coherent with verified product + Phase-0 ADR-007/012/014 and RBAC-Design):
- Single **OWNER** operator with one provisioned account; offline-first field entry is primary.
- Driver & Labourer remain **org catalogue records + data fields** in V1 — NOT app-login roles.
- ADMIN (delegation), DRIVER and LABORER **self-service logins**, driver job-status self-workflow, cross-role notifications, and ADMIN delegation are **deferred to V2** (gated D-1/D-2/D-6).
- Firebase (Auth + Firestore + Functions + Storage + App Check + Crashlytics) present per Phase 0; owner-centred scope.
- Any decision that would re-open scope (e.g., "roles in V1") is listed as blocking.

---

## D-1 — Driver / Labourer self-service (separate app accounts)
- Question: Do drivers and labourers get their own login + role home + own-data journeys in V1, or are they recorded entities managed by the owner?
- Current evidence: Verified product is single-operator; driver = string on Trip; labourer = entity + attendance; **no accounts, no roles**. Roles are PROPOSED (RBAC-Design assumes all roles exist but flags D-1). No evidence of driver/labourer device usage exists.
- Options: (A) V1 owner-only; driver/labourer = records (recommended). (B) V1 includes driver/labourer self-service. (C) V2.
- Recommended: **Option A** for V1; defer B to V2.
- Consequences: V1 has one role (OWNER). No labourer/driver auth screens, no per-role nav, no role-scoped Firestore rules needed in V1 (single owner scoping only), no assignment-to-driver FCM.
- Security: eliminates cross-role attack surface in V1; single-owner rules simplest.
- UX: owner enters attendance (as today).
- Backend/DB: no `users.role` branching in V1 rules; labourer/driver catalogues scoped by owner only.
- Testing: RBAC tests limited to OWNER; driver/labourer self-service tests deferred.
- FINAL DECISION: **RECOMMENDED-DEFAULT = Option A (owner-only V1).** **BLOCKING BUSINESS DECISION** — requires owner confirmation; if roles-in-V1 chosen, V1 scope/DB/rules/notifications change materially.

## D-2 — Admin delegation (ADMIN role / multiple operators)
- Question: Do we ship ADMIN delegation in V1?
- Evidence: single operator today; RBAC-Design keeps ADMIN optional "D-2 scope".
- Options: (A) no ADMIN in V1, role reserved (recommended). (B) owner + delegated ADMIN in V1.
- Recommended: **A**.
- Consequences: one role enforced; user-management UX collapses to the single owner account; `users.role` still stored (default OWNER) but only one value active.
- Security/UX/Backend/DB/Testing: simplest; ADMIN reserved enum, no ADMIN UI/rules branches in V1.
- FINAL DECISION: **RECOMMENDED-DEFAULT = A.** **BLOCKING BUSINESS DECISION** (owner sign-off). If B, add ADMIN UI + delegation tests.

## D-3 — Migration of existing Hive data (legacy `.labourbackup`/Hive)
- Question: Must V1 import existing production data from the current Flutter app?
- Evidence: No evidence production installs/data exist (UNVERIFIED OQ-1/3); current data lives in Hive on-device; `.labourbackup` JSON export exists (FR-15/16). Native app is a fresh install (different package/storage).
- Options: (A) no automated legacy import in V1 (recommended). (B) build one-time `.labourbackup` importer. (C) not needed.
- Recommended: **A** by default; provide a documented, tested `.labourbackup` **importer as a V1-optional utility** IF and only if D-3 confirms real data; never fake it.
- Consequences: If real data exists, omission causes manual re-entry. Importer must validate + roll back (reference semantics) and not overwrite cloud data blindly.
- FINAL DECISION: **BLOCKING BUSINESS DECISION** — confirm whether existing users/data must be migrated. Default = no migration in V1.

## D-4 — Product / package / branding identity
- Question: What is the native package id, app display name, and owner identity? (OQ-2: "Ramesh Sahu" vs package `com.roshan.labourparty`.)
- Evidence: Reference package `com.roshan.labourparty`; name "Labour Party"; "Ramesh Sahu" NOT in source. Brand identity UNVERIFIED.
- Options: (A) retain working id/name for continuity (recommended unless rebranding). (B) new identity.
- Recommended: **A** for continuity of sideload/update on the owner's existing device; owner identity is a provisioning parameter, never hardcoded.
- Consequences: package/applicationId is fixed at scaffold; changing later breaks update path. Play App Signing requires a key (SEC-1 remediation).
- FINAL DECISION: **BLOCKING BUSINESS DECISION** — owner must confirm display name + package + signing identity before scaffold. Implementation may scaffold behind this gate only in a private branch using a throwaway id; do not ship.

## D-5 — Session boundary (Morning/Evening)
- Question: What defines the Morning/Evening session split and is it fixed or configurable?
- Evidence: **Verified FR-03 / DateTimeUtils.getCurrentSession()**: hour 4–11 → Morning, else Evening (i.e. Morning 04:00–11:59, Evening 12:00–03:59). Reference field workType defaulted "Sand".
- Options: (A) keep fixed boundary (recommended). (B) configurable session labels/boundaries.
- Recommended: **A** in V1 (matches verified behaviour); store boundary in business settings for future, but ship fixed.
- Consequences/Backend/Testing: unit-test the boundary; do not silently change it.
- FINAL DECISION: **RESOLVED** — preserve verified boundary (Morning 04:00–11:59, Evening 12:00–03:59). Configurable sessions DEFERRED (non-blocking).

## D-6 — Driver work-status workflow (assigned→accepted→in progress→completed)
- Question: Do we model a driver-driven status workflow in V1?
- Evidence: Net-new concept; depends on D-1. Today owner records trips directly; `trip.status` is a stored string with no real lifecycle in the reference (audit: default-string issue).
- Options: (A) owner-driven minimal trip lifecycle in V1 (open session → trips recorded; session closed); no assigned/accepted states (recommended). (B) full driver state machine (V2).
- Recommended: **A**. Trip `status` in V1 = minimal owner-owned enum with owner-only transitions; full state machine DEFERRED to D-6/V2.
- FINAL DECISION: **RECOMMENDED-DEFAULT = A** (owner-driven). **BLOCKING BUSINESS DECISION** if B is desired in V1 (tied to D-1). The FINAL-WORK-STATE-MACHINE below documents owner-driven transitions as authoritative for V1 and the driver leg as OUT-OF-SCOPE.

## D-7 — Reports scope
- Question: What reports/analytics ship in V1?
- Evidence: Verified analytics = KPIs (works, trips, top driver) + sortable trip data table (FR-14); grouped history (FR-13); `.labourbackup` backup (FR-15). Advanced payroll/wage ledger has no requirement.
- Recommended: **V1 = replicate existing analytics** (KPIs + sortable table + grouped history) + CSV export of the filtered/queried data + secure backup. Advanced/payroll/custom reports **DEFERRED**.
- Consequences: no wage aggregation (no wage stored) unless a confirmed need appears.
- FINAL DECISION: **RESOLVED (recommended)** — confirm report set with owner before V1 ship is a NON-BLOCKING confirmation.

## D-8 — Data retention & privacy
- Question: Retention window, backup/delete semantics, PII handling?
- Evidence: PII = labourer/driver names + optional phone (PRD risk: plaintext backup). No telemetry. Region: India (operator), company-internal use.
- Recommended: owner **owns data**; no automatic deletion; keep records until owner deletes; **encrypted + signed** backup (fix plaintext risk); deletion of a labourer/driver = soft-delete preserving historical attendance (do not rewrite history); notification retention (if any) 30 d; no analytics PII; document data-sovereignty note.
- FINAL DECISION: **RESOLVED (recommended defaults)** — exact retention/legal window to be confirmed by owner as NON-BLOCKING.

---

## Consolidated status
- Resolved (source basis): D-5; D-7 (recommendation); D-8 (defaults).
- Recommended-default + **BLOCKING BUSINESS DECISION** (need owner sign-off): D-1, D-2, D-3, D-4, D-6 (via D-1), and the overall scope S-V1.
- Because the V1 scope default itself and D-1/D-2/D-3/D-4 require explicit owner confirmation that is **not present**, the Phase-0.75 readiness is **NOT READY** (blocking scope/branding/migration decisions). All documents below are fully specified **contingent on S-V1** so that, once the owner confirms S-V1 and D-3/D-4, implementation has no remaining product/architecture ambiguity.

## References
- Verified product scope: `docs/01-PRODUCT/PRD.md` §1,§5; FR/NFR.
- Roles: `docs/04-SECURITY/RBAC.md`, `docs/08-NATIVE-ANDROID/RBAC-DESIGN.md`.
- Session boundary: FR-03 / `DateTimeUtils`.
- Phase-0 decisions: `docs/08-NATIVE-ANDROID/ADR/007|008|009|010|012|013|014`.
