# UI Inventory

Purpose: Register every user-visible screen, reusable component, dialog/sheet and stateful UI pattern in the current app. Full per-screen behaviour in `SCREENS.md`. Status: VERIFIED (code-traced). Commit `2dd2fe4`.

## A. Routed screens
| S-ID | Screen | Route | Class/file | Bottom-nav visible |
|---|---|---|---|---|
| S-01 | Splash | `/splash` | `SplashScreen` (`features/dashboard/.../splash_screen.dart`) | no |
| S-02 | Dashboard | `/dashboard` | `DashboardScreen` | yes (tab 0) |
| S-03 | Work Details | `/details` | `DetailsScreen` | **no** (orphaned route) |
| S-04 | History | `/history` | `HistoryScreen` | yes (tab 1) |
| S-05 | Analytics | `/analytics` | `AnalyticsScreen` | yes (tab 2) |
| S-06 | Settings | `/settings` | `SettingsScreen` | yes (tab 3) |
| S-07 | Trip Details | `/trip-details` | `TripDetailsScreen` | no (pushed) |
| S-08 | Add Trip / Add-Edit Work | `/add-edit-work` | `AddEditWorkScreen` | no (pushed) |
| S-09 | Confirm Next Trip | `/confirm-next-trip` | `ConfirmNextTripScreen` | no (pushed) |

## B. Shared/reusable components (`lib/shared/widgets`)
| C-ID | Component | File | Used by |
|---|---|---|---|
| C-01 | `EmptyStateWidget` | `empty_state.dart` | dashboard, history, analytics, (trip details no-data) |
| C-02 | `GlassCard` | `glass_card.dart` | everywhere (dashboard cards, lists, forms sections) |
| C-03 | `CustomTextField` | `custom_text_field.dart` | add-edit, confirm-next-trip, dialogs |
| C-04 | `PremiumButton` | `premium_button.dart` | primary CTAs (confirm, save, etc.) |
| C-05 | `SkeletonContainer` | `skeleton_loading.dart` | loading skeleton |
| C-06 | `MainLayout` | `main_layout.dart` | bottom-nav shell for 4 tabs |

## C. Dialogs / sheets / transient (VERIFIED)
| D-ID | Element | Trigger | Type |
|---|---|---|---|
| D-01 | Delete latest trip confirm | dashboard minus | AlertDialog |
| D-02 | Delete specific trip confirm | swipe (dashboard/details), history delete | AlertDialog |
| D-03 | Add Labour | trip-details `person_add` | AlertDialog |
| D-04 | Edit Labour | trip-details edit | AlertDialog |
| D-05 | Restore summary + overwrite confirm | settings restore | AlertDialog |
| D-06 | Error dialogs (backup/restore invalid/large/unsupported) | settings restore | AlertDialog |
| D-07 | Save/delete/undo success | various | SnackBar (+ Undo action on labour remove) |
| D-08 | Unsaved draft prompt | add-edit open | SnackBar with Restore |

No bottom sheets exist. No drawer/hamburger exists (bottom nav only).

## D. State UI patterns
| Pattern | Where | Notes |
|---|---|---|
| Skeleton loading | dashboard body (`_buildSkeleton`) | glass skeleton w/ shimmer |
| Full-screen spinner | history/analytics/settings/trip-details/details | `CircularProgressIndicator` |
| Empty state | dashboard/history/analytics/trip-details | EmptyStateWidget or inline text |
| Error text | dashboard/trip-details/details/history/analytics | centred error-colour text; mostly no retry |
| Pull-to-refresh | dashboard | RefreshIndicator |

## E. Key UI gaps (VERIFIED / PARTIAL)
- No drawer/hamburger; navigation is bottom-nav only (4 tabs) — spec target 4–5 is satisfied.
- `/details` (S-03) not reachable from any visible nav; search & filter actions inert in it.
- Settings "Theme Mode" and "About App" tiles are inert (`onTap: () {}`).
- Empty-state widgets on History/Analytics show an **empty labelled button** (ctaText `''`) because the shared component always renders a CTA.
- Filtering reachable only through state, not UI.
- Only dark theme.
