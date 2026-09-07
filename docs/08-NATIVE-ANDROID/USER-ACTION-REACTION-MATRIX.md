# User Action → Reaction Matrix

Status: Phase 0.5 (PROPOSED). For every interactive element define action → feedback → validation → state → backend → outcome → navigation → notification. Shared components make this consistent app-wide.

## Common reactions (design-system contract)
| Action | Immediate feedback | Validation | State | Backend | Success | Failure | Nav | Notification |
|---|---|---|---|---|---|---|---|---|
| Tap primary CTA (Save/Confirm) | ripple; button→spinner | inline | Submitting | op | Success; auto-dismiss | typed Error snackbar+retry | return/origin | per op |
| Tap Delete (row) | → confirm dialog | — | awaiting confirm | (owner/CF) | toast + undo | error | stay | none |
| Swipe-to-delete | red reveal; snap | — | confirm dialog | soft-delete CF | undo offered | error | stay | audit |
| Tap Retry | spinner | — | Loading | retry op | success | error persists | stay | — |
| Tap notification | (foreground) none | — | — | mark read | opens deep link | if unreadable → Forbidden msg | deep-linked screen | read state |
| Tap Back | system/animated | if dirty → autosave/confirm | — | — | pop | discard confirm | previous | — |
| Pull-to-refresh | spinner top | — | Refreshing | refetch | fresh | offline note / error | stay | — |
| Tap + (new) | opens create | — | — | — | — | — | create screen | — |
| Long-press (row) | context menu | — | — | — | — | — | actions | — |
| Tap filter | sheet | — | — | query | applied | — | stay | — |
| Tap search | field focus | min length | Searching | query (debounced) | results | empty/error | stay | — |

## Per-feature reactions
### Tap Save on New Work Session / Trip (N-24/25)
Feedback: spinner on Save. Validation: form rules (§17) + ≥1 labour + driver. State: Submitting. Backend: CF/rules create (authoritative number). Success: snackbar → pop → N-20 refresh. Failure: validation inline OR error(conflict/offline) with retry; if offline → queued, show "pending sync". Notification: none (internal). Audit: creation.
### Tap Save attendance (N-23)
Feedback: per-toggle immediate; batch Save. Validation: labour roster membership; transitions. Backend: writes (offline queue if offline). Success: confirm + counts updated. Partial failure: saved ones kept, failed flagged for retry (no fake success). Audit: changes/corrections.
### Tap Delete trip (N-20/N-22)
Confirm dialog → owner/CF soft-delete + cascade attendance → audit → refresh; undo where reversible; if a labourer references → preserve history.
### Tap "Assign to driver" (N-24/N-50 gated D-1/D-6)
Owner picks driver → CF validates → updates trip → notify driver (FCM) → driver sees assignment.

## Undefined reactions flagged
| Element | Gap |
|---|---|
| Concurrent edit conflict banner (two admins edit same session) | Needs explicit reaction (merge/refresh prompt) |
| Back with unsaved NEW changes vs autosaved draft | Confirm discard vs keep draft semantics |
| Notification centre row offline | Mark-read offline → queue? (decide; recommend queue/retry) |
| Report export big dataset | Needs progress + cancel (loading op) |

## Verification
Shared contract + per-feature reactions PROPOSED. Each becomes a Compose UI test + interaction test. See USER-ACTION-REACTION in EDGE/flow docs and TEST-COVERAGE-MATRIX.
