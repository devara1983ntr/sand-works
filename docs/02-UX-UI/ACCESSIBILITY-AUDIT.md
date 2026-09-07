# Accessibility Audit (current)

Purpose: audited findings with severity & remediation. Commit `2dd2fe4`.

Legend: [VERIFIED] = observed in code/design tokens; numeric claims left as `UNVERIFIED` because no emulator/TalkBack was runnable in this environment.

| ID | Sev | Finding | Evidence | Remediation |
|---|---|---|---|---|
| A-1 | HIGH | Icon-only buttons lack semantic labels/tooltips (search, +labour, edit, delete, refresh, add next/remove trip, presence switches on some rows). | dashboard/trip-details/history/details screens | Add Semantics/ContentDescription; Tooltips |
| A-2 | HIGH | Low-alpha body text (white70/54/38) on slate-800 bg risks sub-AA contrast. | theme tokens; widget TextStyles | Raise to AA (≥4.5:1 body, 3:1 large); audit tokens |
| A-3 | MED | Sub-48dp icon targets in list rows & table. | history edit/delete 20px icons; DataTable | Enlarge hit areas (min 48dp) |
| A-4 | MED | Small-screen text scale overflow not validated. | minTextAdapt config | Test at 1.3–2.0 font scale |
| A-5 | MED | Decorative looping animations run unconditionally; no reduced-motion respect. | flutter_animate repeat() on FAB/counter/skeleton | Honor OS reduced motion; disable decorative loop |
| A-6 | LOW | Colour not the only signal for presence (name strikethrough also used) — acceptable but inconsistent; some statuses colour-only. | trip-details rows | Use icon/label + colour |
| A-7 | MED | Brand images have no semantic description. | splash & app-bar Image.asset | Add semantics label |
| A-8 | LOW | Loading/empty/error announced only as text; not all announce to screen readers distinctly. | various | Compose accessibility announcements |

## Overall
Accessibility is a **later-stage concern** in the current product (present but not systematically implemented). Because the current app is text/form-centric, basic TalkBack may work, but icon buttons and contrast need work. Native product must build accessibility into architecture (see `02-UX-UI/ACCESSIBILITY.md`).

## Verification status
Static findings VERIFIED. Dynamic (TalkBack) validation `UNVERIFIED`.
