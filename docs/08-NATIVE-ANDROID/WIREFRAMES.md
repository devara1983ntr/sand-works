# Wireframes (ASCII)

Status: Phase 0.5 (PROPOSED). Low–mid-fidelity ASCII wireframes for important screens with app bar/back/content/actions and major state variations. No production code. More detailed layout in ANDROID-DESIGN-SYSTEM; this is the structural/mapping reference.

Legend: ( · ) = icon; [..] button; ☰ menu; ≡ drawer.

## N-20 Owner Dashboard (compact)
```
┌──────────────────────────────┐
│ [≡]  Today · 12 Aug 2026    [ ]│  app bar (date picker/search)
│ ┌──────────────────────────┐ │
│ │ Day: Morning ▾  (session)│ │
│ │ No. work Sessions: 3     │ │
│ ├──────────────────────────┤ │
│ │ ( · ) Trip #1 · Tractor A│ │  card
│ │   Driver: Raju ▸         │ │
│ │   5 labour · 4 present   │ │
│ ├──────────────────────────┤ │
│ │ ( · ) Trip #2 ...        │ │
│ └──────────────────────────┘ │
│           ( + New trip )     │  FAB
├──────────────────────────────┤
│ [Dashboard][Work][Reports][More]│  bottom nav (owner)
└──────────────────────────────┘
State variants: loading=skeleton cards; empty="No work today [Record first trip]"; 
error="[Retry]"; offline=banner above content "Offline — showing saved copy";
syncing=top bar "Syncing… (2 pending)"; forbidden=not reachable by non-owner.
```

## N-23 Trip detail + attendance (owner)
```
┌──────────────────────────────┐
│ [‹ Back]  Trip #1        [Edit]│
│ Session: 12 Aug Morning      │
│ Tractor: Tractor A  Driver: Raju
│ Place: Site 12    Type: Sand  │
│ ┌ Attendance (4/5 present) ─┐│
│ │[+ Add labour]             ││
│ │ ( ✓ ) Ramu     present    ││
│ │ ( ✓ ) Shyam   present     ││
│ │ (   ) Gopal   absent      ││
│ │ ...                        ││
│ └───────────────────────────┘│
│        [ Save attendance ]   │
└──────────────────────────────┘
States: loading skeleton; offline→queued "pending sync"; partial-failure→rows failed
flagged [Retry]; conflict→banner "Changed by someone else [Reload]"; submitting→button spinner.
```

## N-24/25 New/Edit Work Session + Trip (owner)
```
┌──────────────────────────────┐
│ [‹ Back]  New Work Session   │
│ Date     : [ 12-08-2026 ]    │  picker
│ Session  : (•)Morning ( )Evening│  segmented
│ Work type: [Sand ▾]          │  drop-down
│ Status   : Open (auto)       │
│ ───────── Trip ───────────── │
│ # (auto 4)   [Tractor ▾]     │
│ Driver   : [Raju ▾] required │
│ Place    : [Site 12]         │
│ [ + Add labour ]  roster chips │
│                  [ Save ]    │
│            * Session already exists for this date ▾  (conflict warn)
└──────────────────────────────┘
States: validation inline error under field; conflict uniqueness banner; offline queued label;
unsaved=autosave indicator; submitting spinner; success→pop+refresh.
```

## N-27 Reports (owner, compact → two-pane expanded)
```
┌──────────────────────────────┐
│ [‹/nav]  Reports        [Export]│
│ Period: [This month ▾]  [⇅sort]│
│ ┌ KPI summary ──────────────┐ │
│ │ Labour-days: 142  Trips: 48│ │
│ │ Avg/day: ...  Top site    │ │
│ ├───────────────────────────┤ │
│ │ ▸ 12 Aug: 3 trips, 20 days│ │
│ │ ▸ 11 Aug: 2 trips ...     │ │
│ └───────────────────────────┘ │
│            [ Download CSV ]   │
└──────────────────────────────┘
Expanded: KPI left + date list right / two-pane drill-down. States: skeleton; empty
"Record work to see reports"; export progress + cancel; error retry.
```

## N-33/34 Users & Roles (owner)
```
┌──────────────────────────────┐
│ [‹ Back]  Users          [+Invite]│
│ (role chips filter: All Owner ...)
│ ┌──────────────────────────┐ │
│ │ ( · ) Raju  Driver · Active│ │
│ │      Assigned: Tractor A    │
│ │ ( · ) Ramu  Labourer · Active
│ │ ...
│ └──────────────────────────┘ │
└──────────────────────────────┘
Tap → detail N-34: edit role/status (owner) with confirm + re-auth for destructive.
States: empty "Invite your first user"; forbidden non-owner hidden.
```

## N-38 Backup (owner)
```
┌──────────────────────────────┐
│ [‹ Back]  Backup & Restore    │
│ Cloud backup: (•) On          │
│ Last backup: 11 Aug 14:02     │
│ [ Back up now ]   [progress..]│
│ ── Restore ────────────────── │
│ Available backups: [12-08 v]  │
│ [ Restore ]  (confirm + warn) │
│ Restore does full overwrite.  │
└──────────────────────────────┘
States: backup progress/cancel; restore confirm→progress→success/fail+rollback.
```

## N-40/N-41/N-42 brief
- N-40 Profile: avatar+photo, displayName, phone editable; role/email/org read-only.
- N-41 Notification centre: unread-first list; tap→deep link; read state; empty state.
- N-42 Help/about: FAQ, contact, version; remove inert tiles (reference BUG).
- Gated: N-50/51 driver home+trip; N-60..63 labourer — same templates, own-data scope (D-1/D-6); sketch per SCREEN-BY-SCREEN spec.

## Coverage note
Every critical screen has ≥ default/loading/empty/error/offline/success states in ASCII; role-specific visibility noted. Full state/UI mapping cross-references SCREEN-STATE-MATRIX, EMPTY-STATE-MATRIX, LOADING-STATE-MATRIX, ERROR-STATE-MATRIX, OFFLINE-STATE-MATRIX.
