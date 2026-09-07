# Phase 7 — Cross-cutting: Accessibility, Responsive, Performance, Assets, Localization — Task Contracts

Prerequisites: earlier phases progressively; gates applied at the end. Execution BLOCKED until READY.

Shared refs: `FINAL-ACCESSIBILITY.md`, `FINAL-RESPONSIVE.md`, `FINAL-PERFORMANCE-CONTRACT.md`, AGENT §14.10–14.12, `ANDROID-PERMISSIONS.md`.

### IMPL-701 Accessibility gate implementation
Objective: make every V1 screen meet FINAL-ACCESSIBILITY PASS on all applicable axes (TalkBack order/semantics, ≥48dp targets, content descriptions, focus order, text scaling no clip @200%, contrast AA light+dark, live-region error/async announcements, keyboard reachability, reduced motion, colour-independence).
Why: FINAL-ACCESSIBILITY gate + F-28 parity fix; a11y is not a single page (Phase-0.5 finding).
Source: `FINAL-ACCESSIBILITY.md`.
Expected: semantics assertions + manual TalkBack pass list. Tests: FT-A11Y.
Acceptance: all applicable axes PASS; no colour-only or gesture-only states.
Runs across IMPL-401..606 (verify per screen). Downstream: Gate-7.

### IMPL-702 Responsive / adaptive (M3)
Objective: per window class compact/medium/expanded/landscape/foldable/split-screen — NavigationSuite (NavBar→Rail), two-pane where defined, no clipping, SavedState on resize/config; adaptive per FINAL-RESPONSIVE.
Why: FINAL-RESPONSIVE gate; reference phone-first defect.
Tests: screenshot/layout per breakpoint × orientation × font scale. Acceptance: gates pass.

### IMPL-703 Performance contract
Objective: implement PERF contract — indexes/pagination (no full scans), off-main DB/network, baseline profile, macrobenchmark targets, counters via CF (no client aggregate), no fake perf.
Source: `FINAL-PERFORMANCE-CONTRACT.md`, `FINAL-QUERY-RULE-MATRIX.md`.
Tests: FT-PERF macrobenchmark. Acceptance: targets measured/confirmed or measurement method recorded.

### IMPL-704 Asset inventory & brand asset resolution
Objective: establish authoritative logo/icon/brand/avatar/font assets per ANDROID-DESIGN-SYSTEM; no placeholders/fabrication.
Source: AGENT §14.10–14.12; `FINAL-DECISION-REGISTER.md` D-4.
Expected: `ASSET-INVENTORY` (this dir or DESIGN-SYSTEM) with Asset ID/Purpose/Source/Licence/format/dimensions/path/status/fallback.
Blockers: if an authoritative asset is absent → **BLOCKED — MISSING ASSET**; report; do NOT invent a logo/icon (AGENT §14.10/14.11).
Acceptance: every icon/logo intentional; no emoji/random Material-icon-as-brand.

### IMPL-705 Localization string resources
Objective: externalise all copy into string resources with plurals; RTL-safe; locales per D-? (F-29).
Source: AGENT §14.13; `FINAL-ACCESSIBILITY.md`; localization coverage.
Expected: `values*/strings.xml`, plurals; default locale English; further locales as approved.
Acceptance: no hardcoded user-visible strings; fabricated copy avoided (truthful content only).
