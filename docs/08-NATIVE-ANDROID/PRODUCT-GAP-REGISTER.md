# PRODUCT GAP REGISTER (Master)

Status: Phase 0.5 (PROPOSED). Single aggregation artifact for every gap across the Phase 0/0.5 docs. Columns: GAP-ID, Category, Feature, Screen, Current/expected, Missing behaviour, Impact, Severity, Priority, Recommendation, Source/Evidence, Status.
Severity: CRITICAL/HIGH/MEDIUM/LOW. Status: OPEN / DECISION / INCLUDE(native) / NOT-CARRY.

> Blocking business decisions D-1..D-8 remain authoritative placeholders; every gap that depends on them is marked DECISION and must not be treated as answered.

## CRITICAL
| GAP | Category | Feature/Screen | Summary | Impact | Severity | Priority | Recommendation | Source | Status |
|---|---|---|---|---|---|---|---|---|---|
| G-001 | Decision | Driver/labourer self-service login+app | Is driver & labourer self-service in scope? | Gates N-11/12, N-50..53, N-60..63, RBAC driver/labourer, attendance confirm | CRITICAL | P0 | Confirm D-1 before any driver/labourer UI | RBAC-DESIGN, MISSING-SCREENS | DECISION |
| G-002 | Decision | Admin role & delegation | Beyond single owner, are ADMIN users and delegation in scope? | Gates N-11(admin), audit access model, org model | CRITICAL | P0 | Confirm D-2 | RBAC-DESIGN | DECISION |
| G-003 | Security | Role & status fields client-writable | Reference/user doc allows client to write role/status → privilege escalation | CRITICAL security | CRITICAL | P0 | Server/CF-only writes + rules; never client | FIRESTORE-AUTHZ A1 | OPEN |
| G-004 | Security | Server-only authority (numbers, uniqueness, sessions) | Reference has client-side numbering/single-user; multi-user needs CF-authoritative sequential numbers + unique open session | Duplicates/invalid state under concurrency | CRITICAL | P0 | CF transactions + authoritative numbers (BO-1/2) | DATABASE/BCK | OPEN |
| G-005 | Security | Client audit forgeable | Client-generated audit unreliable | Non-repudiation loss | CRITICAL | P0 | Server(CF) generates audit atomically; client read-only | AUDIT-LOG | OPEN |
| G-006 | Data | Attendance history/correction lost | Reference overwrites attendance; no before/after/reason | History loss + no accountability | CRITICAL | P0 | Versioned attendance + reason + audit (BO-3) | DATABASE, AUDIT-LOG | OPEN |
| G-007 | Decision | Driver status workflow scope | Assign/accept/start/complete for driver gated on D-6 | Unknown driver work lifecycle | CRITICAL | P0 | Confirm D-6 before driver UI/BO-6/7 | WORKFLOW/E2E | DECISION |

## HIGH
| GAP | Category | Feature/Screen | Summary | Impact | Severity | Priority | Recommendation | Source | Status |
|---|---|---|---|---|---|---|---|---|---|
| G-101 | Data | orgId/ownerId scoping | Reference is single-user; native multi-owner/multi-org needs org scoping on every doc | cross-org leakage | HIGH | P1 | Add orgId+createdBy everywhere; rules scope | DATABASE | OPEN |
| G-102 | Concurrency | Optimistic `rev` missing | No version field → lost updates on multi-admin edits | data loss | HIGH | P1 | `rev` + optimistic concurrency + Conflict UI | CONCURRENCY, EDGE E9 | OPEN |
| G-103 | Concurrency | Idempotency keys | Retries/double-submit/offline replay duplicate ops | dupes/side effects | HIGH | P1 | Server-side opId dedupe; disable during submit | CONCURRENCY, BCK | OPEN |
| G-104 | Product | Conflict-resolution UI | Two-admin / offline-online divergences need resolution (E9/E16/E25) | silent overwrite | HIGH | P1 | Define prompt-not-overwrite resolution + test | CONCURRENCY, EDGE | OPEN |
| G-105 | Product | Session close op/screen | Reference has no explicit session close; closed-session immutability window (D-8) undefined | state integrity | HIGH | P1 | Add close op (W6/BO-5) + restricted-correction rule | WORKFLOW/E2E | OPEN |
| G-106 | Data | ≥1 active owner guard | Role change/delete could remove last active owner → lock-out | lock-out | HIGH | P1 | CF guard on BO-10/16 | FIRESTORE-AUTHZ A, BCK | OPEN |
| G-107 | Product | Correction reason required | Attendance/record corrections need a reason field (owner) | accountability | HIGH | P1 | reason field + validation + audit | DATABASE/FORM | OPEN |
| G-108 | Notifications | Full event/recipient/channel model | Reference lists notifications but event set was undefined | incomplete notify | HIGH | P1 | Adopt NOTIFICATION-COVERAGE event register | NOTIFICATION | INCLUDE |
| G-109 | Search/UX | History/filter/sort feature gaps | F-04/05/15 filter & history-wide search unimplemented in reference | missing function | HIGH | P1 | Define complete search/filter/sort (rule-valid) | FEATURE-GAP | INCLUDE |
| G-110 | Data | Firestore query vs rules | Some queries not rule-valid (rules are not post-query filters) | denied/leak | HIGH | P1 | Shape queries to satisfy rules | DATABASE/FIRESTORE-AUTHZ A5 | OPEN |
| G-111 | UX | Session-expiry preserving drafts | Editing work, session expires → must preserve draft/outbox (G2) | lost work | HIGH | P1 | Re-auth preserves draft/outbox | SCREEN-STATE G2 | OPEN |
| G-112 | Product | Offline fake-success prevention | Reference ambiguous about offline save confirmation | user misled | HIGH | P1 | Explicit queued/pending-sync UI; no fake success | OFFLINE | OPEN |
| G-113 | Reports | Report aggregation performance | Full-history client aggregation risky (PERF) | perf | HIGH | P1 | CF/scheduled aggregates + counters (D-7) | REPORTS/FEATURE | INCLUDE(D7) |

## MEDIUM
| GAP | Category | Feature/Screen | Summary | Severity | Priority | Recommendation | Source | Status |
|---|---|---|---|---|---|---|---|---|
| G-201 | UX | Unauthorized-vs-Forbidden visual unification | G1 | MEDIUM | P2 | distinct but consistent states | SCREEN-STATE G1 | OPEN |
| G-202 | UX | Partial failure attendance UI | G3 multi-row partial | MEDIUM | P2 | keep-saved/flag-failed retry | SCREEN-STATE G3 | OPEN |
| G-203 | Offline | Login-offline policy | Allow cached-session auth offline? decision | MEDIUM | P2 | decide | OFFLINE | DECISION |
| G-204 | Offline | Queue visibility/management UI | show pending count, per-item retry/discard | MEDIUM | P2 | define | OFFLINE | INCLUDE |
| G-205 | Offline | Mark-notification-read offline | read semantics offline | MEDIUM | P2 | decide (queue or on-online) | OFFLINE/NOTIFICATION | DECISION |
| G-206 | Data | Denormalised displayName propagation | labourer/driver name rename propagation + old snapshot | MEDIUM | P2 | CF propagation | DATABASE/DATA-CONSIST | INCLUDE |
| G-207 | Backend | Counter/roll-up reconciler | counters drift after partial/offline | MEDIUM | P2 | CF reconciler | DATA-CONSIST | INCLUDE |
| G-208 | Notifications | FCM token/topic lifecycle & cleanup | role change/suspend leaves stale subscribers | MEDIUM | P2 | server mgmt | NOTIFICATION/FIREBASE | INCLUDE |
| G-209 | Data | Session/date boundary value | D-5 fixed boundary for rollover | MEDIUM | P2 | confirm D-5 | BUSINESS-RULE, EDGE E24 | DECISION |
| G-210 | Reports | Report export offline/progress/cancel | undefined export failure/progress | MEDIUM | P2 | LOADING/ERROR on export | LOADING/REPORTS | INCLUDE |
| G-211 | Security | Writable protected fields (createdAt/ownerId/approved/status) | must be server/CF-only; reject client writes | MEDIUM | P2 | rules reject (A6/A8) | FIRESTORE-AUTHZ | OPEN |
| G-212 | UX | Forbidden/NotFound friendly destination for denied deep links | notification/manual deep links to inaccessible target | MEDIUM | P2 | friendly fallback list link | NAVIGATION/NOTIFICATION | INCLUDE |
| G-213 | Responsive | Native must be adaptive by default | reference phone-first | MEDIUM | P2 | M3 NavigationSuite + two-pane | RESPONSIVE/ADR-013 | INCLUDE |
| G-214 | A11y | Per-screen a11y beyond single page | reference one a11y page | MEDIUM | P2 | ACCESSIBILITY-COVERAGE per screen | A11Y | INCLUDE |
| G-215 | Product | Empty-state CTAs when blank | reference BUG-06 blank CTA | MEDIUM | P2 | optional meaningful CTA | EMPTY | INCLUDE |
| G-216 | Product | Swipe affordance correctness | reference BUG-03 misleading delete-swipe | MEDIUM | P2 | match reveal action | GESTURE | INCLUDE |
| G-217 | Product | Inert/about tiles | F-21 unimplemented/remove | MEDIUM | P3 | implement or remove | FEATURE-GAP | INCLUDE |
| G-218 | Product | Search/filter unused handlers (AddQuickTripEvent, ValidationFailure, dup switch) | BUG-02/03/05/12/18 | MEDIUM | P2 | remove/replace during native build | AUDIT | INCLUDE |
| G-219 | Product | Orphan `/details` route / unused routes | don't carry; define real nav | MEDIUM | P2 | clean nav graph | MISSING/NAVIGATION | INCLUDE |

## LOW / DECISION-listed
| GAP | Category | Summary | Severity | Recommendation | Source | Status |
|---|---|---|---|---|---|---|
| G-301 | Security | Andr-M committed keystore (SEC-1) remediation | LOW(phase0) | remove/rotate before release | ANDROID-SECURITY | OPEN |
| G-302 | Report | Backup/export retention & privacy window (D-8) | LOW | decide retention/privacy | FIREBASE/REPORTS | DECISION |
| G-303 | Product | Driver self-service attendance & labourer confirm | D-1 sub-scope | decide | WORKFLOW | DECISION |
| G-304 | Product | Cloud backup & export cloud storage (D-3/D-7) | LOW | decide | FIREBASE | DECISION |
| G-305 | Product | Localization scope / languages | F-29 | decide set | FEATURE-GAP | DECISION |
| G-306 | Product | Payment/2FA decisions (out of requirement) | LOW | confirm out-of-scope | FIREBASE/BUSINESS | DECISION |

## Aggregate view
- Severity counts (Phase 0.5 native product): CRITICAL 7 · HIGH 13 · MEDIUM 19 · LOW/Decision 6.
- DECISION-gated (D-1..D-8): G-001,002,007,209,302,303,304,305,306,203,205 (+ role-specific screens/rows elsewhere).
- Every finding maps to recommendation + source; no TODO/TBD placeholders — unresolved items are OPEN or DECISION with an owner.

## Usage
Implementation must clear CRITICAL/HIGH before claiming readiness. Re-verify at implementation; register is the source of truth for prioritised remediation.
