# Interaction Specification

Purpose: precise interaction behaviours (current VERIFIED) and proposed native deltas. Commit `2dd2fe4`.

## 1. Primary vs secondary vs destructive actions per screen
| Screen | Primary | Secondary | Destructive | Disabled state |
|---|---|---|---|---|
| S-02 Dashboard | Add Work (FAB), + next trip | search, pull-to-refresh | minus remove-latest; swipe delete | minus disabled when 0 trips; delete icon dims when 0 trips |
| S-07 Trip Details | (no primary save) | add/edit labour; presence toggles | remove labour (w/ Undo) | — |
| S-08 Add/Edit | Save Trip | add labour | remove labour row | Save blocked if invalid/0 labours (snackbar) |
| S-09 Confirm Next | Save as Next Trip | Cancel | — | Save blocked if invalid |
| S-06 Settings | — | Backup / Restore | Restore overwrites (confirm) | UI spinner while busy |

## 2. Confirmation behaviour (VERIFIED)
- Destructive deletes always use AlertDialog confirm (Cancel/Delete). Delete text red.
- Restore overwrite requires an explicit summary dialog + confirm.
- Labour removal does **not** confirm but provides an Undo Snackbar (3 actions: Undo re-saves).

## 3. Feedback patterns
- Success: SnackBars ("Trip added successfully", "Trip Saved Successfully!", "Backup successful…", "Restore successful!").
- Errors: snackbars (form) or centred text (load errors) or dialogs (backup/restore typed errors).
- Loading: skeleton (dashboard) / full-screen spinners (others) / disabled button spinner (PremiumButton isLoading).

## 4. Keyboard & focus
- Search field focus handled; inline TextField autofocus on Details screen.
- Forms scroll to keep fields visible; `adjustResize` window soft input.
- No explicit focus-traversal customisation.

## 5. Validation UX
- Inline field validators (required) shown by TextFormField error style.
- Cross-field: "At least one labour is required." snackbar on save.
- Backup/restore: file-type & size validated with targeted dialogs.

## 6. Proposed native interaction deltas (PROPOSED)
- `PopScope` to confirm discarding unsaved changes (esp. Confirm Next Trip).
- Snackbars → Material3 Snackbar/Error actions with retry.
- Add explicit retry on all error states.
- Consistent destructive-action pattern (confirm on all permanent deletes, undo where reversible).
- Touch target ≥ 48dp; semantics & focus announcements.
- Expose filter UI (currently only reachable in state layer).
