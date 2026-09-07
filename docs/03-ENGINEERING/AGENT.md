# AGENT.md — Operating rules for autonomous coding agents on this project

Purpose: binding conventions so future agents (and this migration) do not break truth, introduce fake logic, or expose secrets.

Status: VERIFIED project facts; rules are requirements for all future work. Commit `2dd2fe4`.

## 1. Project identity (current)
- Offline-first single-user Flutter app **"Labour Party"**; package `com.roshan.labourparty`; clean-ish architecture + BLoC + Hive + go_router.
- Behavioural reference for a future **native Android** rebuild (Kotlin/Compose/M3/Firebase) — the native app does not exist yet.

## 2. Repository structure (required reading order)
1. `README.md`, root `PRD.md` (product intent).
2. `lib/main.dart`, `lib/routes/app_router.dart`.
3. `lib/features/work/**` (domain/data/bloc).
4. Screen files under `features/*/presentation`.
5. `android/` build & manifest.
6. `docs/` (this audit suite is the authoritative spec for the native rebuild).

## 3. Architecture rules
- Keep Presentation → Domain ← Data dependency direction. **Domain must not depend on Flutter/Hive.**
- Put business rules in usecases/domain; keep Hive/file code in data.
- Do not access Hive from presentation widgets; route through repository/bloc. (The current repo violates this in a few places — those are bugs to avoid repeating, not precedent.)
- One bloc per aggregate where justified; sealed states; exhaustive switches.

## 4. Coding & naming conventions
- Dart: `lowerCamelCase` methods/fields, `UpperCamelCase` types, `_`-private members, `sealed` state/event classes, `flutter_lints` (`analysis_options.yaml`).
- Keep files focused; one widget/class per concern where reasonable.
- Never duplicate a shared model across two files (e.g., the duplicated `LabourFormModel` is a smell — extract shared model).

## 5. Dependency rules
- Keep dependencies minimal & justified (offline-first now).
- Regenerate Hive adapters with `hive_generator`/`build_runner` when models change; do not hand-edit `.g.dart`.
- No runtime network fetch for fonts in an offline app — bundle fonts.

## 6. UI rules
- Preserve the app's functional UX; maintain consistent use of the theme/components.
- No invented screens; each screen must be reachable & behave per `SCREENS.md`.
- No inert controls (empty `onTap`/`onChanged` handlers) unless documented.

## 7. Security rules
- **Never commit secrets/keys/credentials.** Do not reproduce any secret value in docs/logs.
- `.jks`/`.keystore`/`key.properties` must never be committed; also watch for renames/backups like `*.jks.bak`.
- Do not log PII, tokens, auth material.
- Client-side role checks are never authorization.

## 8. Firebase rules (native work)
- Role/RBAC enforced in backend security rules + Cloud Functions; client only displays.
- Firestore/Storage rules authored & tested; App Check enabled; privileged ops via Cloud Functions; audit logs for admin actions.

## 9. Testing requirements
- Unit tests for usecases/blocs/repositories; widget tests per screen; integration tests for data integrity (trip numbering, restore rollback, date partition); error/offline paths.
- **Mocks only inside isolated tests.** Never fall back to fake data in production.

## 10. Prohibited behaviour (mandatory)
- **Never invent requirements.**
- **Never use fake production logic** (dummy backend, hardcoded accounts, mock data posing as real).
- **Never introduce TODO/FIXME/TBD/XXX placeholders as a substitute for implementation.**
- **Never claim a feature is complete without verification.**
- **Never bypass security rules.**
- **Never expose secrets.**

## 11. Commit & PR requirements
- Meaningful, reviewable commits; branch per concern; no secrets; no generated-file hand edits.
- PRs must include evidence of test/analyze run and reference affected FR/BR IDs.
- Do not modify application source solely to "clean up" during an audit-only phase; document any unavoidable build change.

## 12. Documentation requirements
- Keep docs truthful; separate VERIFIED / INFERRED / UNVERIFIED / MISSING / BROKEN / PARTIAL / PROPOSED.
- Update CHANGELOG only for real changes; never pretend the native app exists.

## 13. Native implementation agent contract (Phase 0.75 — binding for the coding agent)
The implementation agent MUST follow the final specification package under `08-NATIVE-ANDROID/` (`PRODUCT-FREEZE.md`, `IMPLEMENTATION-CONTRACT.md`, and the full `FINAL-*` set). Specifically:
> The implementation agent MUST NOT invent product behavior.
> The implementation agent MUST follow the final specification.
> If the specification is ambiguous, the agent MUST stop and report the ambiguity.
> The implementation agent MUST NOT use fake production data.
> The implementation agent MUST NOT create fake authentication.
> The implementation agent MUST NOT bypass Firebase Security Rules.
> The implementation agent MUST NOT treat client-side RBAC as security.
> The implementation agent MUST NOT introduce undocumented dependencies.
> The implementation agent MUST NOT silently change business rules.
> The implementation agent MUST add tests for implemented behavior.
> The implementation agent MUST update documentation when approved behavior changes.

Scope: build MUST/selected SHOULD HAVE only (PRODUCT-FREEZE). Do NOT implement OUT OF SCOPE or DEFERRED items (driver/labourer self-service, ADMIN, driver workflow, payroll, legacy import) until the respective decisions (D-1/D-2/D-6/D-3) are owner-approved.

## 14. Zero-Placeholder, Zero-Fabrication & Complete-Implementation Policy (BINDING)

This is a strict, machine-checkable engineering policy. It governs ALL code, UI, resources, assets, configuration, docs representing implemented behaviour, and tests that validate production behaviour. It applies to the current Flutter reference AND the native Android rebuild. When this policy conflicts with the temptation to make something "look finished", the policy wins.

### 14.1 Absolute rule
The repository must contain only real, intentional, production-quality implementation. The agent MUST NOT use placeholders, dummy implementations, fake data, fabricated content, temporary UI, simulated backend behaviour, incomplete logic, or misleading success states merely to make a feature appear finished. **If a required implementation cannot legitimately be completed from the available requirements, source code, connected services, assets, or authoritative documentation, STOP and report the blocker.** Never fabricate an implementation to bypass a blocker.

### 14.2 Zero-placeholder policy — prohibited tokens & content
Prohibited in production code, UI, resources, assets, configuration, and tests of production behaviour: `TODO`, `FIXME`, `TBD`, `WIP`, `XXX`, `HACK`, `TEMP`, `TEMPORARY`, `PLACEHOLDER`, `PLACEHOLDER_TEXT`, `COMING SOON`, `UNDER CONSTRUCTION`, `IMPLEMENT LATER`, `ADD LATER`, `REPLACE LATER`, `INSERT HERE`, `YOUR_TEXT_HERE`, `YOUR_NAME_HERE`, `YOUR_LOGO_HERE`, `YOUR_API_KEY_HERE`, `EXAMPLE_VALUE`, `SAMPLE_VALUE`, `DUMMY_VALUE`, `FAKE_VALUE`, `MOCK_VALUE`, `TEST_VALUE` (when used as production data), lorem ipsum, arbitrary filler text, and fabricated names/phones/addresses/business records/statistics/analytics/financial values/dates/notifications/user accounts/backend responses. Do not merely rename placeholders to bypass this rule. Examples such as `John Doe`, `1234567890`, `test@test.com`, `Sample User`, `Demo Company`, or arbitrary invented business data are prohibited when presented as real application data. Legitimate occurrences in **this documentation describing the policy** or in **negative test cases** are not violations (see §14.21).

### 14.3 No fake data
Never manufacture data to make screens/dashboards/reports/analytics/history/lists/maps/notifications/profiles appear populated. Production UI displays only: (1) real persisted data, (2) real backend data, (3) legitimate user-entered data, (4) authoritative static product content, or (5) a truthful empty state. If there is no data, show the correct empty state. WRONG: seeding `Work("John Doe","Construction",2500)` when no such record exists. CORRECT: `No work records yet. Add a work record to get started.` An empty state must not pretend records exist.

### 14.4 No fake backend
Never simulate backend functionality in production: no hardcoded repository responses, no fabricated Firestore documents, no fake authentication, no bypassing Firebase Security Rules, no pretending sync succeeded, no fabricated notification delivery or cloud-function responses, no returning `true` merely to simulate success, no silently swallowing backend failures, and no replacing unavailable backend behaviour with fake local behaviour unless an approved architecture decision explicitly allows it. A feature requiring Firebase/backend must be built against the approved architecture (see `08-NATIVE-ANDROID/IMPLEMENTATION-CONTRACT.md`, `FINAL-BACKEND-CONTRACT.md`, `FINAL-FIREBASE-SECURITY-MODEL.md`). If the backend contract is unavailable/insufficient, STOP → document the blocker → do not fake the backend.

### 14.5 No fake authentication or authorization
Authentication and authorization must be real. Never hardcode a logged-in user or an owner/admin role, never auto-bypass login, never create a fake authenticated session, never trust a client-provided role, never hide authorization failures, never grant access merely because a UI element is hidden. Authorization is enforced at the authoritative layer (Firestore/Storage Security Rules + Cloud Functions). **UI visibility is NOT authorization.**

### 14.6 No fake success
Never display success when the operation did not succeed. Every mutating operation explicitly distinguishes: `Idle`, `Validating`, `Submitting`, `Succeeded`, `Failed`, `Offline/Pending`, `Conflict`, `Unauthorized`, `Cancelled`. Do NOT `onClick { showSuccess() }` unless the operation actually completed. Offline-capable operations must communicate pending synchronization rather than claiming server success. (See `08-NATIVE-ANDROID/FINAL-UI-STATE-CONTRACT.md`, `FINAL-OFFLINE-SYNC.md`.)

### 14.7 No incomplete code
Never commit knowingly incomplete code. Prohibited: `TODO()`, `error("Not implemented")`, `throw NotImplementedError()`, `return null` used only to bypass required implementation, empty function bodies/handlers, dead buttons, dead navigation destinations, inert form submissions, fake repository methods, unfinished ViewModels/state reducers, partial DB/Firebase operations, commented-out production functionality, and unreachable code used to hide missing implementation. A compiler-clean build is NOT automatically a complete implementation.

### 14.8 No `// TODO` bypass
Never use `// TODO`, `// FIXME`, `// implement later`, `// temporary`, `// replace this`, `// add real implementation` as a substitute for implementation. If something is required for the current scope, implement it. If outside the current scope, document it as deferred/out-of-scope in the authoritative specification (PRODUCT-FREEZE), NOT hidden in code as a TODO.

### 14.9 No placeholder UI
Every visible UI element must have an intentional purpose and real behaviour. Do not create empty cards just to fill space, fake statistics, decorative buttons/tabs/search/filters/switches/nav destinations/charts/avatars/profile info/notification counts/badges/maps/images, fake loading shown forever, placeholder icons, or generic components copied to make a screen look complete. A visible element must (1) perform its specified behaviour, (2) represent genuine state, (3) provide intentional navigation, or (4) be a legitimate non-interactive design element.

### 14.10 No placeholder logos or branding
Never invent or approximate the official logo/brand/icon/wordmark. Do not use generic or random generated logos, Unicode/emoji as logo substitutes, temporary Material icons as permanent brand marks, unrelated stock logos, text pretending to be a logo, or AI-generated approximations when an authoritative asset exists. Use the repository's authoritative brand assets (see `08-NATIVE-ANDROID/ANDROID-DESIGN-SYSTEM.md` + brand assets). If the required official asset does not exist, STOP and report the missing asset. Do not silently invent a replacement.

### 14.11 No placeholder icons
Every icon has a deliberate semantic purpose. Do not replace a required custom icon with emoji, random glyphs, unrelated Material icons, Unicode symbols, or text characters. If the spec requires a custom asset that is unavailable, report it rather than fabricate one.

### 14.12 No placeholder images
Never use arbitrary images to make a screen look finished (stock/Unsplash/random/AI-generated images, screenshots pretending to be real content, fabricated profile or business imagery). Use only authoritative project assets, legitimately sourced permitted assets, user-provided assets, or intentionally designed non-content surfaces. If an image is optional and none exists, implement the specified fallback state rather than inventing content.

### 14.13 No fabricated copy
All user-visible text must be intentional and traceable to approved product requirements, authoritative design specs, verified existing behaviour, approved UX copy, or necessary standard system wording. Do not invent business claims, statistics, guarantees, legal statements, pricing, operational policies, or feature descriptions. When copy is genuinely undefined, use a truthful neutral state only when appropriate; otherwise flag the missing product decision.

### 14.14 No silent feature dropping
Never silently omit a required feature because it is difficult. Classification: REQUIRED → implement; DEFERRED → do not implement, document as deferred; BLOCKED → STOP and report; OPTIONAL → implement only if explicitly approved. Never convert REQUIRED into unimplemented without authorization.

### 14.15 No silent architectural substitution
Do not replace an approved architecture with a shortcut (e.g., Firebase → hardcoded local data, Firestore → static JSON, real auth → local boolean, Room → in-memory list, WorkManager → fire-and-forget when durable work is required, server authorization → UI visibility, Cloud Function → client-side privileged mutation, real sync → fake "synced"). Any architectural deviation requires an explicit documented decision (see `08-NATIVE-ANDROID/ADR/`).

### 14.16 No fake error handling
Do not catch errors merely to keep the app appearing functional. Prohibited: `catch (_) { /* ignore */ }` and silently converting failures into success. Errors must be represented in state, logged appropriately, observable, actionable where possible, and communicated honestly to the user. Never hide an operational failure. (See `FINAL-ERROR-CONTRACT.md`.)

### 14.17 No fake loading
Loading indicators correspond to real async work. No loading forever, no artificial delays for effect, no loading with no operation occurring, no hiding missing implementation behind a spinner. Every loading state has a real initiating operation and a deterministic completion/failure path. (See `FINAL-UI-STATE-CONTRACT.md`.)

### 14.18 No fake tests
Tests validate actual behaviour. No tests that assert hardcoded expected values without exercising the implementation; do not mock away the entire feature under test, test fake repositories instead of real contracts where integration is required, emit `assertTrue(true)`, disable/skip tests to get green, or weaken assertions to hide defects. Tests may use controlled fixtures/mocks/fakes only where appropriate to the testing layer; test doubles never leak into production. (See `08-NATIVE-ANDROID/FINAL-TEST-CONTRACT.md`.)

### 14.19 No disabled quality gates
Never bypass quality controls for a green build: no disabling lint, suppressing meaningful warnings, disabling/excluding tests or modules, skipping static analysis, weakening security rules, bypassing auth, removing validation, suppressing crashes, removing assertions, or altering CI to appear green. A green CI obtained by disabling a check is a failure.

### 14.20 No visual cover-up
Never optimise for "looks finished" over "is finished". A polished screen with fake data, dead interactions, fabricated metrics, or non-functional controls is incomplete. Visual completeness ≠ implementation completeness.

### 14.21 Traceability requirement
Every implemented feature must be traceable: Requirement → Use Case → State → UI → Domain Logic → Repository → Data Source → Security/Authorization → Error/Offline Behaviour → Tests (see `08-NATIVE-ANDROID/FINAL-TRACEABILITY-MATRIX.md`). If a link is missing, identify it explicitly.

### 14.22 Definition of done
A feature is NOT done merely because it compiles, renders, navigates, has a button, looks polished, has a test, or shows a mock response. It is DONE only when ALL applicable requirements are satisfied: real implementation; real state transitions; validation; persistence/backend integration where required; authorization; truthful loading/success/failure states; offline & conflict behaviour where required; accessibility; tests cover critical behaviour; no prohibited placeholders; no dead required interactions; no known incomplete paths.

### 14.23 Missing-information protocol
When implementation depends on unavailable information, the agent MUST NOT guess. Classify as exactly one of: `BLOCKED — BUSINESS DECISION`, `BLOCKED — DESIGN SPECIFICATION`, `BLOCKED — MISSING ASSET`, `BLOCKED — BACKEND CONTRACT`, `BLOCKED — CREDENTIAL/ENVIRONMENT`, `BLOCKED — EXTERNAL DEPENDENCY`, `BLOCKED — SECURITY REQUIREMENT`. Then report: (1) what is missing, (2) why it is required, (3) what implementation is blocked, (4) the smallest decision/input needed to continue.

### 14.24 Required pre-commit scan
Before declaring a task complete, scan changed files + relevant scope for prohibited tokens: `TODO FIXME TBD WIP XXX HACK TEMP TEMPORARY PLACEHOLDER DUMMY FAKE SAMPLE LOREM NOT IMPLEMENTED NOTIMPLEMENTED COMING SOON UNDER CONSTRUCTION` — plus semantic equivalents that don't contain those exact words. A textual grep is NOT sufficient; also perform a semantic review for fake data, fake UI, dead interactions, hardcoded backend results, fabricated assets, fake auth, fake success, incomplete state machines, silent error handling, and unfinished code. Legitimate occurrences in documentation describing this policy or in negative test cases do not constitute violations.

### 14.25 Final hostile review (before reporting completion)
- Data: Is every displayed value real or legitimately derived? Did I fabricate anything?
- UI: Does every visible interactive element work? Any placeholder component/asset?
- Branding: Are all logos/icons/images authoritative and intentional?
- Logic: Does every state transition have real behaviour? Are all validation/conditional paths implemented?
- Backend: Does every backend operation use the approved architecture? Did I simulate anything?
- Security: Can a client bypass an authorization decision? Did I trust client-controlled role/status/security fields?
- Errors: Can the app claim success when the operation failed? Are failures silently swallowed?
- Offline: Can the app falsely claim synchronization? Are pending/conflict states truthful?
- Code quality: Is any required function incomplete? Any TODO/FIXME/TBD markers deferring required work?
- Tests: Do tests exercise real behaviour? Did I weaken/disable a quality gate?
If ANY answer indicates fabrication, incompleteness, deception, or an unapproved shortcut, DO NOT declare the task complete.

### 14.26 Agent behaviour
The agent is explicitly authorised and required to say: **"I cannot safely implement this without additional authoritative information."** This is preferable to inventing functionality. Priority order (never reversed): Correctness > Truthfulness > Security > Architectural integrity > Completeness > UX polish > Speed.

### 14.27 Hard failure conditions
The task is considered FAILED if the agent introduces fake production data or placeholder UI, fabricates branding or images/icons, creates fake authentication or backend behaviour, reports success for a failed operation, leaves required functionality incomplete, uses TODO/FIXME/TBD as a substitute for implementation, silently drops requirements, bypasses security, disables tests/quality gates, hides errors, or declares completion despite a known blocker. A build that compiles while violating these rules is NOT acceptable.

### 14.28 Final completion statement
When reporting completion the agent MUST state, with evidence:

```
IMPLEMENTATION STATUS: COMPLETE
PLACEHOLDER POLICY: PASS
FAKE/DUMMY DATA POLICY: PASS
REQUIRED FEATURE COMPLETENESS: PASS
BACKEND INTEGRATION: VERIFIED / NOT APPLICABLE
AUTHORIZATION: VERIFIED / NOT APPLICABLE
VALIDATION: VERIFIED
ERROR STATES: VERIFIED
OFFLINE STATES: VERIFIED / NOT APPLICABLE
TESTS: PASS
STATIC ANALYSIS: PASS
SECURITY CHECKS: PASS
KNOWN BLOCKERS: NONE
```

If any item cannot truthfully be marked PASS, report `IMPLEMENTATION STATUS: NOT COMPLETE` and list the exact blockers. Never claim PASS without evidence.

**FINAL DIRECTIVE:** This policy is mandatory. Do not optimise for something that merely looks finished. Produce software that is actually finished, truthful, secure, testable, traceable, and production-ready. When forced to choose between (A) fabricating something to keep moving and (B) stopping and reporting the missing requirement, always choose B.

### 14.29 Policy immutability & anti-bypass rule
The Zero-Placeholder, Zero-Fabrication & Complete-Implementation Policy (§14) is binding and immutable during normal implementation work. The coding agent MUST NOT: remove or weaken this policy; shorten or reinterpret it to permit prohibited behaviour; delete prohibited-token checks; disable semantic completeness checks; modify the Definition of Done to make incomplete work appear complete; remove blocker classifications; suppress findings produced by these rules; move prohibited implementation into another file to evade scanning; rename prohibited concepts solely to bypass detection; or modify this `AGENT.md` to circumvent these requirements.

**Policy changes require explicit authorization.** Any change to §14 or its enforcement rules requires: (1) an explicit user-approved engineering decision, (2) documentation of the reason for the change, (3) preservation of the original intent wherever possible, and (4) re-running the full implementation-quality audit after the change. An autonomous coding agent MUST NOT authorize its own exception.

**No self-granted exceptions.** The agent may not reason: "This is only temporary", "This is necessary to get the build passing", "We will replace it later", "The user probably expects this", "It is only a prototype", "The placeholder is harmless", "The backend can be connected later", "The asset can be replaced before release". None of these constitute authorization. If the required implementation cannot currently be completed correctly, the agent must use the MISSING INFORMATION PROTOCOL (§14.23) and stop the affected work rather than create an exception.

**Enforcement priority.** This policy takes precedence over: speed, convenience, visual completeness, minimizing diff size, minimizing implementation effort, achieving an apparently green build, and satisfying an arbitrary task deadline. The agent must prefer a truthful blocker over fabricated progress.

**Final integrity condition.** A task cannot be considered complete if completion required violating, bypassing, weakening, or circumventing this policy. No implementation agent may declare its own violation acceptable.

