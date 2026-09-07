# Documentation Index — LABOUR-PARTY-2 Audit

## Purpose
Master index of the documentation suite produced for the Principal Forensic Audit of the existing Flutter reference application and the native Android rebuild specification.

## Audit metadata
- Repository: https://github.com/Manash07Bhoi/LABOUR-PARTY-2.git
- Commit audited: `2dd2fe4ed85e9f0e2a420e3a383fed1e75b8a21b`
- Branch: `main`; audit working branch `audit/documentation`
- Tags present: `RC-1.3`, `v1.0.0`, `v1.0.0-rc1`, `v1.0.1-hotfix-rc1`
- Audit date: 2026-09-07
- Environment: no Flutter/Dart SDK available → static/evidence audit; runtime `UNVERIFIED`

## Quick-start
1. Read `00-AUDIT-INDEX.md` (executive control document + audit conclusion).
2. For product truth read `01-PRODUCT/PRD.md` then `PRD2.md`.
3. For engineering truth read `03-ENGINEERING/CURRENT-ARCHITECTURE.md`.
4. For the native rebuild spec read `08-NATIVE-ANDROID/ANDROID-REBUILD-PRD.md` → `IMPLEMENTATION-ROADMAP.md`.

## Document tree
| Path | Contents |
|---|---|
| `DOCUMENTATION-INDEX.md` | this file |
| `00-AUDIT-INDEX.md` | executive audit control document, findings roll-up, conclusion |
| `01-PRODUCT/PRD.md` | product requirements (current) |
| `01-PRODUCT/PRD2.md` | operational/behavioural spec |
| `01-PRODUCT/PRODUCT-SCOPE.md` | in/out scope |
| `01-PRODUCT/USER-PERSONAS.md` | personas & roles |
| `01-PRODUCT/USER-JOURNEYS.md` | journeys |
| `01-PRODUCT/FEATURE-CATALOG.md` | feature inventory (F-IDs) |
| `01-PRODUCT/FUNCTIONAL-REQUIREMENTS.md` | FR register |
| `01-PRODUCT/NON-FUNCTIONAL-REQUIREMENTS.md` | NFR register |
| `01-PRODUCT/BUSINESS-RULES.md` | business/data rules (BR) |
| `01-PRODUCT/SOP.md` | standard operating procedures |
| `02-UX-UI/UX-AUDIT.md` | UX audit roll-up |
| `02-UX-UI/UI-INVENTORY.md` | UI inventory (screens/components) |
| `02-UX-UI/SCREENS.md` | every screen detailed (S-IDs) |
| `02-UX-UI/SCREEN-SPECIFICATION.md` | per-screen behavioural spec |
| `02-UX-UI/UX-FLOWS.md` | flow diagrams (Mermaid) |
| `02-UX-UI/NAVIGATION-MAP.md` | route register & navigation |
| `02-UX-UI/INTERACTION-SPECIFICATION.md` | interactions |
| `02-UX-UI/GESTURES.md` | gesture audit |
| `02-UX-UI/DESIGN-SYSTEM.md` | design system current + native |
| `02-UX-UI/DESIGN-SYSTEM-AUDIT.md` | current visual audit |
| `02-UX-UI/ACCESSIBILITY.md` | accessibility current + native |
| `02-UX-UI/ACCESSIBILITY-AUDIT.md` | accessibility findings |
| `03-ENGINEERING/ARCHITECTURE.md` | architecture overview |
| `03-ENGINEERING/CURRENT-ARCHITECTURE.md` | detailed current architecture |
| `03-ENGINEERING/CODEBASE-MAP.md` | repository map |
| `03-ENGINEERING/MODULE-DEPENDENCY-MAP.md` | module/feature deps |
| `03-ENGINEERING/DATABASE.md` | Hive database (boxes/schema) |
| `03-ENGINEERING/DATA-MODEL.md` | entities & models |
| `03-ENGINEERING/API.md` | remote/local API reality |
| `03-ENGINEERING/API-SPECIFICATION.md` | repository contract spec |
| `03-ENGINEERING/STATE-MANAGEMENT.md` | BLoC state audit |
| `03-ENGINEERING/STORAGE-ANALYSIS.md` | storage/encryption analysis |
| `03-ENGINEERING/THIRD-PARTY-DEPENDENCIES.md` | dependencies |
| `03-ENGINEERING/ANDROID-PLATFORM.md` | Android host config |
| `03-ENGINEERING/AGENT.md` | agent operating rules |
| `04-SECURITY/SECURITY.md` | security overview |
| `04-SECURITY/SECURITY-AUDIT.md` | security findings |
| `04-SECURITY/THREAT-MODEL.md` | STRIDE threat model |
| `04-SECURITY/SECRETS-AUDIT.md` | secrets/keystore findings |
| `04-SECURITY/AUTHENTICATION-AUTHORIZATION.md` | auth/authz current+native |
| `04-SECURITY/RBAC.md` | RBAC matrix |
| `04-SECURITY/PRIVACY-DATA-FLOW.md` | PII & data flow |
| `05-PERFORMANCE/PERFORMANCE.md` | performance overview |
| `05-PERFORMANCE/PERFORMANCE-AUDIT.md` | performance findings |
| `05-PERFORMANCE/STARTUP-ANALYSIS.md` | startup analysis |
| `05-PERFORMANCE/MEMORY-ANALYSIS.md` | memory analysis |
| `05-PERFORMANCE/NETWORK-ANALYSIS.md` | network (none) |
| `05-PERFORMANCE/PERFORMANCE-OPPORTUNITIES.md` | opportunities |
| `06-QUALITY/TESTING.md` | testing overview |
| `06-QUALITY/TESTING-AUDIT.md` | testing findings |
| `06-QUALITY/TEST-COVERAGE-MAP.md` | coverage map |
| `06-QUALITY/BUG-DEFECT-REGISTER.md` | defect register |
| `06-QUALITY/TECHNICAL-DEBT.md` | technical debt |
| `06-QUALITY/QUALITY-RISKS.md` | quality risks |
| `06-QUALITY/ERROR-STATES.md` | error/empty/offline states |
| `06-QUALITY/PRE-RELEASE.md` | pre-release gate |
| `07-OPERATIONS/CI-CD.md` | CI/CD |
| `07-OPERATIONS/DEPLOYMENT.md` | deployment/release |
| `07-OPERATIONS/OBSERVABILITY.md` | observability |
| `07-OPERATIONS/MONITORING.md` | monitoring |
| `07-OPERATIONS/INCIDENT-RESPONSE.md` | incident response |
| `07-OPERATIONS/BACKUP-RECOVERY.md` | backup & recovery |
| `07-OPERATIONS/CHANGELOG.md` | changelog of this audit |
| `08-NATIVE-ANDROID/ANDROID-REBUILD-PRD.md` | native rebuild PRD (draft) |
| `08-NATIVE-ANDROID/ANDROID-ARCHITECTURE.md` | recommended native architecture (Phase-0 reconciled) |
| `08-NATIVE-ANDROID/ANDROID-DESIGN-SYSTEM.md` | native design system (M3 stable) + design review |
| `08-NATIVE-ANDROID/NATIVE-UX-REDESIGN.md` | native UX redesign |
| `08-NATIVE-ANDROID/NATIVE-NAVIGATION.md` | native navigation architecture (Phase 0) |
| `08-NATIVE-ANDROID/DOMAIN-MODEL.md` | domain entities & model boundaries (Phase 0) |
| `08-NATIVE-ANDROID/PRODUCT-RECONCILIATION.md` | reconciliation audit (Phase 0) |
| `08-NATIVE-ANDROID/RBAC-DESIGN.md` | RBAC capability matrix (Phase 0) |
| `08-NATIVE-ANDROID/FIREBASE-ARCHITECTURE.md` | Firebase service design |
| `08-NATIVE-ANDROID/FIREBASE-DATABASE.md` | Firestore schema design (Phase 0 prior) |
| `08-NATIVE-ANDROID/FIREBASE-DATABASE-DESIGN.md` | Firestore per-collection schema (Phase 0) |
| `08-NATIVE-ANDROID/FIREBASE-SECURITY-RULES.md` | rules design (Phase 0 prior) |
| `08-NATIVE-ANDROID/FIREBASE-SECURITY-RULES-DESIGN.md` | Firestore/Storage rules design (Phase 0) |
| `08-NATIVE-ANDROID/ANDROID-SECURITY-ARCHITECTURE.md` | native security architecture (Phase 0) |
| `08-NATIVE-ANDROID/ANDROID-TESTING-ARCHITECTURE.md` | native testing architecture (Phase 0) |
| `08-NATIVE-ANDROID/BACKEND-CONTRACT.md` | backend/API domain contract (Phase 0) |
| `08-NATIVE-ANDROID/MIGRATION-STRATEGY.md` | Hive→native migration strategy (Phase 0) |
| `08-NATIVE-ANDROID/DEPENDENCY-POLICY.md` | dependency & version policy (Phase 0) |
| `08-NATIVE-ANDROID/OFFLINE-SYNC-ARCHITECTURE.md` | offline/sync architecture (Phase 0) |
| `08-NATIVE-ANDROID/NOTIFICATION-ARCHITECTURE.md` | FCM notification architecture (Phase 0) |
| `08-NATIVE-ANDROID/OBSERVABILITY-ARCHITECTURE.md` | observability architecture (Phase 0) |
| `08-NATIVE-ANDROID/CI-CD-ARCHITECTURE.md` | CI/CD & environment strategy (Phase 0) |
| `08-NATIVE-ANDROID/FLUTTER-TO-ANDROID-MIGRATION-MATRIX.md` | migration matrix |
| `08-NATIVE-ANDROID/FEATURE-PARITY-MATRIX.md` | feature parity |
| `08-NATIVE-ANDROID/ANDROID-PERMISSIONS.md` | permissions audit (native) |
| `08-NATIVE-ANDROID/IMPLEMENTATION-ROADMAP.md` | roadmap (Phase-0/0.5 gated) |

### Phase 0.5 — Product coverage & gap audit (coverage/spec; no code)
| `08-NATIVE-ANDROID/COMPLETE-SCREEN-INVENTORY.md` | complete screen inventory N-01..N-63 (types) |
| `08-NATIVE-ANDROID/SCREEN-BY-SCREEN-SPECIFICATION.md` | per-screen specification template + specs |
| `08-NATIVE-ANDROID/SCREEN-STATE-MATRIX.md` | per-screen Loading/Empty/Error/Offline/… matrices |
| `08-NATIVE-ANDROID/MISSING-SCREENS.md` | missing/orphan screens & orphan `/details` |
| `08-NATIVE-ANDROID/FEATURE-GAP-ANALYSIS.md` | feature chain gap analysis (F-IDs) |
| `08-NATIVE-ANDROID/PRODUCT-GAP-REGISTER.md` | MASTER gap register (G-001.., severities) |
| `08-NATIVE-ANDROID/CONDITIONAL-LOGIC-MATRIX.md` | every condition true/false + reactive |
| `08-NATIVE-ANDROID/BUSINESS-RULE-MATRIX.md` | business rules × role authorization |
| `08-NATIVE-ANDROID/USER-ACTION-REACTION-MATRIX.md` | action→reaction→nav→notification contract |
| `08-NATIVE-ANDROID/NAVIGATION-COMPLETENESS.md` | nav graph + Back + bottom-nav audits |
| `08-NATIVE-ANDROID/USER-JOURNEY-COMPLETE.md` | complete journeys per role + recovery |
| `08-NATIVE-ANDROID/WORKFLOW-SPECIFICATION.md` | business workflows + missing steps |
| `08-NATIVE-ANDROID/GESTURE-SPECIFICATION.md` | gesture register + a11y alternatives |
| `08-NATIVE-ANDROID/FORM-VALIDATION-SPECIFICATION.md` | form + cross-field + server validation |
| `08-NATIVE-ANDROID/ERROR-STATE-MATRIX.md` | error taxonomy + per-screen coverage |
| `08-NATIVE-ANDROID/LOADING-STATE-MATRIX.md` | async loading states |
| `08-NATIVE-ANDROID/EMPTY-STATE-MATRIX.md` | empty states with actions per role |
| `08-NATIVE-ANDROID/OFFLINE-STATE-MATRIX.md` | online/offline/stale/syncing/conflict per feature |
| `08-NATIVE-ANDROID/DATABASE-COMPLETENESS.md` | data schema gaps + state machines |
| `08-NATIVE-ANDROID/BACKEND-COMPLETENESS.md` | backend-op register (BO-1..16) completeness |
| `08-NATIVE-ANDROID/FIREBASE-COVERAGE-MATRIX.md` | Firebase service coverage decisions |
| `08-NATIVE-ANDROID/FIRESTORE-AUTHORIZATION-MATRIX.md` | collection×op×role field-level authz |
| `08-NATIVE-ANDROID/NOTIFICATION-COVERAGE.md` | notification event register + channels |
| `08-NATIVE-ANDROID/ACCESSIBILITY-COVERAGE.md` | per-screen TalkBack/a11y contract |
| `08-NATIVE-ANDROID/RESPONSIVE-UI-SPECIFICATION.md` | M3 adaptive/responsive layout |
| `08-NATIVE-ANDROID/WIREFRAMES.md` | ASCII wireframes per screen + states |
| `08-NATIVE-ANDROID/SYSTEM-FLOWS.md` | Mermaid system/architecture flows |
| `08-NATIVE-ANDROID/END-TO-END-FLOWS.md` | complete E2E scenarios + traceability gaps |
| `08-NATIVE-ANDROID/EDGE-CASE-AUDIT.md` | runtime edge cases E1..E25 |
| `08-NATIVE-ANDROID/CONCURRENCY-AUDIT.md` | idempotency/transactions/optimistic concurrency |
| `08-NATIVE-ANDROID/DATA-CONSISTENCY-AUDIT.md` | multi-record atomic/eventual/rollback models |
| `08-NATIVE-ANDROID/ANALYTICS-EVENT-SPECIFICATION.md` | analytics events (no PII) |
| `08-NATIVE-ANDROID/AUDIT-LOG-SPECIFICATION.md` | audit log (server-generated) spec |
| `08-NATIVE-ANDROID/TEST-COVERAGE-MATRIX.md` | feature×role×scenario test coverage |
| `08-NATIVE-ANDROID/ADR/001-kotlin.md` … `015-testing.md` | Architecture Decision Records |

### Phase 0.75 — Final design closure gate (scope freeze + implementation contract)
| `08-NATIVE-ANDROID/FINAL-DECISION-REGISTER.md` | resolves D-1..D-8; blockers marked |
| `08-NATIVE-ANDROID/PRODUCT-FREEZE.md` | V1 scope MUST/SHOULD/COULD/OUT/DEFERRED |
| `08-NATIVE-ANDROID/FINAL-FEATURE-CATALOG.md` | V1 feature catalog (statuses) |
| `08-NATIVE-ANDROID/FINAL-SCREEN-CATALOG.md` | V1 screen catalog + shared contract |
| `08-NATIVE-ANDROID/FINAL-SCREEN-STATE-MATRIX.md` | per-V1-screen state matrix |
| `08-NATIVE-ANDROID/FINAL-CONDITIONAL-LOGIC.md` | condition true/false spec |
| `08-NATIVE-ANDROID/FINAL-BUSINESS-RULES.md` | R-01..R-90 rules |
| `08-NATIVE-ANDROID/FINAL-WORK-STATE-MACHINE.md` | session/trip/attendance state machine |
| `08-NATIVE-ANDROID/CONCURRENCY-SPECIFICATION.md` | idempotency/rev/txn/conflict |
| `08-NATIVE-ANDROID/ATTENDANCE-INTEGRITY.md` | append-immutable attendance |
| `08-NATIVE-ANDROID/AUDIT-LOG-INTEGRITY.md` | server-generated audit |
| `08-NATIVE-ANDROID/SERVER-AUTHORITY-MATRIX.md` | client-must-not-trust fields |
| `08-NATIVE-ANDROID/FINAL-DATABASE-SCHEMA.md` | implementation-ready schema |
| `08-NATIVE-ANDROID/FINAL-QUERY-RULE-MATRIX.md` | rule-valid queries + indexes |
| `08-NATIVE-ANDROID/FINAL-RBAC-MATRIX.md` | OWNER matrix (others deferred) |
| `08-NATIVE-ANDROID/SECURITY-ATTACK-REVIEW.md` | AT-1..20 hostile review |
| `08-NATIVE-ANDROID/FINAL-NAVIGATION.md` | nav graphs + Back finalization |
| `08-NATIVE-ANDROID/FINAL-GESTURES.md` | gesture register + a11y alt |
| `08-NATIVE-ANDROID/FINAL-FORMS.md` | form + cross-field + server validation |
| `08-NATIVE-ANDROID/FINAL-ERROR-CONTRACT.md` | error taxonomy contract |
| `08-NATIVE-ANDROID/FINAL-UI-STATE-CONTRACT.md` | loading/empty/offline contract |
| `08-NATIVE-ANDROID/FINAL-WIREFRAMES.md` | V1 ASCII wireframes + states |
| `08-NATIVE-ANDROID/FINAL-ACTION-REACTION-MATRIX.md` | action→reaction |
| `08-NATIVE-ANDROID/FINAL-E2E-FLOWS.md` | E2E flows + data-flow diagrams |
| `08-NATIVE-ANDROID/FINAL-BACKEND-CONTRACT.md` | B-01..B-14 backend contract |
| `08-NATIVE-ANDROID/FINAL-FIREBASE-SECURITY-MODEL.md` | rules/Storage/App Check/CF/function boundary |
| `08-NATIVE-ANDROID/FINAL-OFFLINE-SYNC.md` | offline/sync final contract |
| `08-NATIVE-ANDROID/FINAL-ACCESSIBILITY.md` | a11y gate |
| `08-NATIVE-ANDROID/FINAL-RESPONSIVE.md` | responsive gate |
| `08-NATIVE-ANDROID/FINAL-PERFORMANCE-CONTRACT.md` | measurable performance |
| `08-NATIVE-ANDROID/FINAL-TEST-CONTRACT.md` | test contract |
| `08-NATIVE-ANDROID/FINAL-TRACEABILITY-MATRIX.md` | requirement→…→test |
| `08-NATIVE-ANDROID/IMPLEMENTATION-CONTRACT.md` | primary coding-agent contract |
| `08-NATIVE-ANDROID/FINAL-READINESS-REPORT.md` | hostile review + readiness + §44 report |

### Phase 0.8 — Implementation control system (`docs/09-IMPLEMENTATION/`)
Execution-control layer between the specification and the coding agent (planning only; no code).
| `09-IMPLEMENTATION/README.md` | purpose, source-of-truth, readiness (BLOCKED) |
| `09-IMPLEMENTATION/IMPLEMENTATION-MASTER-PLAN.md` | programme objectives, phase model, scope |
| `09-IMPLEMENTATION/IMPLEMENTATION-ROADMAP.md` | ordered roadmap + task inventory (52 tasks) |
| `09-IMPLEMENTATION/IMPLEMENTATION-RULES.md` | binding execution rules (no-code-until-READY) |
| `09-IMPLEMENTATION/PHASE-GATES.md` | Gate-0..Gate-8 completion gates |
| `09-IMPLEMENTATION/TASK-MANAGEMENT.md` | task statuses, small-step model, blocker protocol |
| `09-IMPLEMENTATION/TRACEABILITY-MATRIX.md` | feature/requirement → task mapping |
| `09-IMPLEMENTATION/DEPENDENCY-MATRIX.md` | hard/soft deps, critical path, parallel groups |
| `09-IMPLEMENTATION/RISK-REGISTER.md` | programme risks |
| `09-IMPLEMENTATION/DECISION-REGISTER.md` | Gate-0 blockers BLK-01..07 + non-blocking |
| `09-IMPLEMENTATION/CHANGE-CONTROL.md` | controlled-change process |
| `09-IMPLEMENTATION/PROGRESS-TRACKER.md` | live task-state counters |
| `09-IMPLEMENTATION/COMPLETION-REGISTER.md` | evidence-based completions |
| `09-IMPLEMENTATION/IMPLEMENTATION-CONTROL-AUDIT.md` | §27/§28 quality audit + final output |
| `09-IMPLEMENTATION/tasks/` | task template + `PHASE-1..8` full task contracts |

## Consolidated PDF
`docs/AUDIT-REPORT.pdf` — generated from the completed documentation.

## Classification legend
VERIFIED / INFERRED / UNVERIFIED / MISSING / BROKEN / PARTIAL / PROPOSED / DEPRECATED. See `00-AUDIT-INDEX.md`.
