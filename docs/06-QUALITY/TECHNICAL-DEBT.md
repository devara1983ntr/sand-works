# Technical Debt Register

Status: VERIFIED. Commit `2dd2fe4`. Only substantive debt listed (not style preferences).

## Debt by category
| Category | Item | Ref |
|---|---|---|
| Architectural | Presentation widgets reach into Hive (dashboard labour lookup; add-edit draft) breaking layering | `dashboard_screen.dart`, `add_edit_work_screen.dart` |
| Architectural | Business aggregation in screens (history grouping, analytics KPIs) | history/analytics |
| Architectural | Backup/restore data logic embedded in a settings screen | `settings_screen.dart` |
| Architectural | Navigation-as-state via `NavigateToConfirmNextTripState` + multi-emit | `work_bloc.dart` |
| Architectural | Filtering stored in bloc private mutable fields, not state | `work_bloc.dart` |
| Code | Duplicate `LabourFormModel` top-level class in two screens | both create screens |
| Code | Duplicate `@override` annotations; duplicate/overlapping switch cases | data source, repo impl, trip_details |
| Code | Empty handlers / dead events (`AddQuickTripEvent`), dead `ValidationFailure`, orphaned `/details` | bloc, failures, router |
| Dependency | `google_fonts` runtime network fetch vs offline-first | app_theme |
| Persistence | No schema migration framework; JSON-in-string draft; deterministic work-id semantics ambiguity | DATABASE/STORAGE |
| Persistence | Hive plaintext; no compaction/retention | |
| UI | Two overlapping "add trip" entry flows (Add Work vs Next Trip) create conceptual overlap | add_edit vs confirm_next_trip |
| UI | Dark-only, non-tokenized styles, fixed 360×690 design | theme/screenutil |
| Testing | `restore_logic_test` tests rules indirectly; missing tests for settings/details/draft/confirm screens | TEST-COVERAGE-MAP |
| Security | Committed keystore-like file (item treated as bug/security debt BUG-01) | SECRETS-AUDIT |
| Infra | No CI/CD, no automated quality gate in repo | 07-OPERATIONS |
| Docs | Pre-existing agent docs may over-claim execution/readiness vs current verification | CODEBASE-MAP note |

## Prioritisation suggestion
1. Security debt (BUG-01, encryption, backup integrity).
2. Data-integrity & migration readiness (schema versioning, semantics of deterministic work id).
3. Code-debt cleanup (duplicates/dead code/inert UI) — small, safe, high value.
4. Layering refactors — do with the native rebuild rather than risky mid-life Flutter churn.
5. CI/test & documentation truth alignment.

## Verification status
VERIFIED entries. Some are PROPOSED improvements rather than blocking defects.
