# Accessibility (requirements & current state)

Purpose: audit current accessibility readiness and specify the native target. Status: current gaps VERIFIED (static); numeric checks `UNVERIFIED` (no emulator/TalkBack in sandbox). Commit `2dd2fe4`.

## Current state (VERIFIED)
- App relies on Flutter defaults for semantics; custom components mostly use plain text (some discoverable).
- **No explicit `Semantics` labels**, no `Tooltip`s on icon-only buttons (search, add labour, delete, refresh, plus/minus, edit). TalkBack will announce generic "button" / icon names. (Finding A-1.)
- Brand image on splash & app bar has no semantic description.
- Text contrast: many `Colors.white70/white54/white38` on dark slate surfaces; small text at 38–70 % alpha likely **below WCAG AA** for body sizes (numerical check not run — `UNVERIFIED`). (A-2.)
- Touch targets: some icon buttons within rows are small (e.g., 20px history edit/delete icons; DataTable cells) — likely < 48dp. (A-3.)
- Font scaling: uses `minTextAdapt` + `splitScreenMode`; text scale-up layout not explicitly tested for overflow. (A-4.)
- Reduced motion: entrance/loop animations (`flutter_animate` shimmer/scale/pulse) run unconditionally; **no reduced-motion handling**. (A-5.)
- Colour is used alone for presence (green left border / text colour) — not fully colour-independent (absent also uses strikethrough on name → partially mitigated). (A-6.)
- No screen-reader state announcements (loading/empty/error) beyond text.
- No keyboard-navigation focus management beyond defaults.

## Native accessibility requirements (PROPOSED)
- Compose content descriptions on all icon/image elements; `onClick` semantic roles.
- Touch targets ≥ 48dp (Material guidance) for all interactive elements.
- Colour schemes AA-compliant; text not below AA; support dark+light.
- Respect large-font & system font scale; verify no overflow.
- Support reduced motion (disable decorative animation).
- Provide loading/empty/error/offline semantics announcements to TalkBack.
- State announcements for presence switches & delete.
- Colour-independent status indicators (icon+text+colour).
- Accessibility UI tests (TalkBack/Escalator) on core journeys (see `06-QUALITY/TESTING.md`).

## Verification status
VERIFIED static gaps A-1..A-6. Numeric contrast & TalkBack behaviour `UNVERIFIED` (requires emulator). See `ACCESSIBILITY-AUDIT.md`.
