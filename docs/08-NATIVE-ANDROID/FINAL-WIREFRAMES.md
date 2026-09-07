# FINAL WIREFRAMES — V1 (Phase 0.75)

Every V1 screen is represented below. A shared wireframe template shows each state (Default/Loading/Empty/Error/Offline/Submitting/Success/Unauthorized/Forbidden where relevant); key screens are drawn; compact screens reference the template + FINAL-SCREEN-CATALOG content list. ASCII, no code. Full detail in FINAL-SCREEN-CATALOG + SCREEN-BY-SCREEN (Phase 0.5).

Legend: [btn] button · ( · ) icon · [≡] menu · ▸ expand · spinner `##`.

## Shared state strip (appended to each screen)
Default → content. Loading → skeleton of content shapes. Empty → [EmptyState icon + text + optional primary/secondary action]. Error → [ErrorState + Retry]. Offline → offline banner above + (cached stale | pending). Submitting → primary CTA spinner+disabled. Success → transient snackbar/summary + refresh. Unauthorized → re-auth route. Forbidden → message + allowed area.

## N-03 Login (Default / Submitting / Error)
```
┌──────────────────────────┐
│ [Logo]                    │
│  Email    [________]      │
│  Password [________] (show)│
│        [ Sign in ## ]     │   ##=spinner when submitting
│  Forgot password?  >      │
│  (offline banner)         │
│  Error: "Email or ..." [Retry]
└──────────────────────────┘
```
Provisioning N-07 similar (name + accept) → home.

## N-10 Owner shell (all owner screens mount here)
```
Compact: bottom nav [Dashboard|Work|Reports|More]
Medium+:  Rail (icons) + content; two-pane where defined
```

## N-20 Dashboard (Default/Loading/Empty/Offline/Search)
```
┌──────────────────────────┐
│ [≡] Today · 12-08-2026 [🔍]│  top bar search
│ Session: Morning ▾        │
│ [Skeleton cards]   <-Loading
│ ① Work: 3 trips · 5 labour │  Success cards (tap→detail)
│ ② ...                     │
│ (empty) "No work today [Record first trip]" 
│ (offline) banner "Offline - showing saved copy"
│ [ + New ]  (FAB)
├──────────────────────────┤
│ [Dashboard][Work][Reports][More]│
└──────────────────────────┘
```

## N-23 Trip detail + attendance (Default/Submitting/Partial/Conflict/Correction-reason)
```
┌──────────────────────────┐
│ [‹ Back] Trip #1     [Edit]│
│ Session Morning · Tractor A · Driver Raju · Sand
│ Attendance (4/5 present)   │
│ [+ Add labour]  [✓]Ramu  [✓]Shyam  [ ]Gopal
│ ...                        │
│         [ Save attendance ]│   → Submitting
│ Partial: "2 saved, 1 failed [Retry]"  
│ Conflict banner: "Changed elsewhere [Reload|Keep]"
│ Correction: when editing saved → reason required field
└──────────────────────────┘
```

## N-24 New/Edit Work Session
```
│ [‹ Back] New Work Session   │
│ Date  [12-08-2026 ▾]        │
│ Session (•)Morning ( )Evening│
│ Work type [Sand ▾] Place [__]
│ Status: Open (auto)         │
│           [ Save ]          │   Submitting/queued
│ Duplicate warn: "Open session exists [Open it]" 
```

## N-25 Trip editor / next-trip
```
│ [‹ Back] Trip #4            │  # auto (server)
│ Vehicle [Tractor A ▾] Driver [Raju ▾] req
│ Place [__] Notes [__]       │
│ Labour roster chips [+Add]  │
│ "Next trip" prefill hint    │
│            [ Save / Save next ]│
│ Draft autosaved indicator; Offline→"Queued-pending"
```

## N-27 Reports/Analytics (Default/Loading/Empty/Export)
```
│ Reports [This month ▾] [⇅ sort] [Export CSV]
│ KPI: Works 8 · Trips 31 · Top driver Raju
│ table: Date|Trip#|Driver|Labour|Present   (tap→detail)
│ Empty: "Record work to see reports"
│ Export: progress + Cancel → Success/Error
```
N-22 Day detail: sessions Morning/Evening, per-session trip count, [Close session] w/ confirm.
N-38 Backup/restore, N-29/31/30 catalogues, N-36 audit, N-37 settings, N-39 account, N-40 profile, N-41 notifications, N-42 help: all = standard list/form + shared state strip + SSC content from FINAL-SCREEN-CATALOG; ASCII per template above (app bar/back/content/actions/bottom-nav) with the per-screen content already enumerated in the screen catalog.

## Verification
Every V1 screen maps to a wireframe (drawn or template+content). States Default/Loading/Empty/Error/Offline/Submitting/Success shown; Unauthorized/Forbidden handled via shared strip. Driver/labourer screens excluded (D-1).
