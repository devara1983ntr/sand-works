# Bug & Defect Register

Status: VERIFIED findings (static). Many require device execution to fully reproduce; marked UNVERIFIED where runtime-dependent. Commit `2dd2fe4`. Severity: CRITICAL/HIGH/MEDIUM/LOW/INFORMATIONAL.

Schema fields per row where relevant: ID, Sev, Priority, Area, File, Symbol, Repro, Observed, Expected, Root cause, Evidence, Impact, Security impact, Recommended fix, Status.

| ID | Sev | Priority | Area | File / Symbol | Observed | Expected | Root cause | Impact | Sec impact | Recommended fix | Status |
|---|---|---|---|---|---|---|---|---|---|---|---|
| BUG-01 | CRITICAL | 1 | Security | `android/app/keystore.jks.bak` (tracked, all tags) | Private-key/keystore-like DER file committed | No key material in repo | `.gitignore` misses `*.jks.bak`; force-added commit 55b2144 | Signing-key exposure risk | HIGH (spoofing signed builds) | Rotate key; purge history/branches/tags; CI secret-scan | OPEN |
| BUG-02 | MED | 2 | UX | `details_screen.dart` search `onChanged:(){}` + filter `onPressed:(){}` | Search & filter icons inert | Should filter | Incomplete screen; orphaned `/details` route | Dead/inert UI | none | Wire or remove (DEPRECATED native) | OPEN |
| BUG-03 | MED | 2 | UX | `details_screen.dart` Dismissible | Both backgrounds show blue edit icon; swipe maps endToStart→delete confirm, else→edit | Swipe affordance should match action | Copy-paste backgrounds | Misleading delete affordance | none | Match icon/colour to action | OPEN |
| BUG-04 | MED | 2 | State | `work_bloc.dart` `_onFilterDashboard` / load path | Filter state stored; `matchesFilter` always true; no UI dispatches filter | Filtering works | Filter unimplemented + not surfaced | Filtering dead in UI | none | Implement & expose | OPEN |
| BUG-05 | MED | 2 | State | `work_bloc.dart` `_onAddQuickTrip` | Empty handler (comment says UI navigates) | Remove dead handler | Incomplete refactor | Dead code | none | Remove event/handler | OPEN |
| BUG-06 | LOW | 3 | UX | `empty_state.dart` + history/analytics empty | EmptyStateWidget always draws a button; callers pass `ctaText:''` | No blank button | Shared widget assumes CTA | Blank button in empty state | none | Optional CTA | OPEN |
| BUG-07 | LOW | 3 | Settings | `settings_screen.dart` Theme Mode / About tiles `onTap:(){}` | Inert tiles shown as interactive | Implement or hide | Stubs | Misleading | none | Implement/remove | OPEN |
| BUG-08 | MED | 2 | Consistency | `date_time_utils.dart` vs root `PRD.md` §36 | Code: 04:00–11:59 Morning; PRD text: 00:00–11:59 | Aligned spec | Doc drift | Wrong session for 00–04 | none | Resolve & document | OPEN |
| BUG-09 | MED | 2 | Trip Details | `trip_details_screen.dart` edit action | Edit only works when state is `TripDetailsLoaded`; otherwise silently no-ops | Robust edit | Guard depends on transient state | Silent no-op | none | Decouple edit from current state | OPEN |
| BUG-10 | MED | 2 | Labour edit | `work_bloc.dart` `_onSaveLabour` | Emits WorkActionSuccess, does not reload open trip list; labour name edit may not refresh list | Refresh list | Comment acknowledges | Stale UI | none | Reload affected screen | OPEN |
| BUG-11 | MED | 2 | Duplicate switch | `trip_details_screen.dart` | Duplicate/overlapping switch branches (dead) | Single exhaustive switch | Merge artifacts | Dead code/reliability | none | Clean | OPEN |
| BUG-12 | LOW | 3 | Hygiene | `work_local_data_source.dart`, `work_repository_impl.dart` duplicate `@override` | Duplicate annotations | Single | Merge artifacts | Lint/code smell | none | Clean + lint gate | OPEN |
| BUG-13 | MED | 2 | Restore | `settings_screen.dart` restore | Row-count verification compares length only; no semantic/field validation on injected data | Authenticated/validated restore | No signature | Untrusted data injection | LOW-MED | Add integrity/auth + deep validation | OPEN |
| BUG-14 | LOW | 3 | Error UX | Dashboard/Trip error states | Error text w/o retry path | Recovery path | Missing | Poor recovery | none | Add retry | OPEN |
| BUG-15 | MED | 2 | Security/PII | Backup + Hive plaintext | PII unencrypted | Encrypted | No crypto | Privacy | MED | Encrypt at rest (native) | OPEN (proposed) |
| BUG-16 | LOW | 3 | Data model | `Labour.phoneOptional` | Field unused by UI | Confirm intent or remove | Half-added | Ambiguity | none | Decide | OPEN |
| BUG-17 | LOW | 3 | Nav robustness | `app_router.dart` `state.extra as Trip` | Hard cast; deep-link without extra would throw | Safe nav | Extra-based nav | Crash on invalid nav | LOW | Type-safe nav / defaults | OPEN (UNVERIFIED runtime) |
| BUG-18 | INFO | 4 | Dead code | `ValidationFailure` unused; `AddQuickTripEvent` empty; Details orphan route | Dead code present | Minimal dead code | Incomplete features | Tech debt | none | Prune | OPEN |
| BUG-19 | LOW | 3 | Analytics/History empty | Empty-state blank CTA & HistoryEmpty grouping | Clean empty UI | Shared comp assumption | Blank button | none | Fix | OPEN |

## Notes
- No defects classified as a confirmed runtime crash without device execution; runtime reproduction of BUG-03/09/17 is `UNVERIFIED` here.
- Full native feature gaps (auth/RBAC/notifications/etc.) are tracked as PROPOSED features, not defects of the current offline product.

## Status legend
OPEN = needs fix (no source changes made during this audit-only phase).
