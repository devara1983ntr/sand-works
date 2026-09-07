# Loading State Matrix

Status: Phase 0.5 (PROPOSED). Every async operation with its loading UI. Rule: avoid blocking the whole screen for localized ops; use localized indicators; keep prior content on refresh.

| Operation | Where | Loading UI | Blocking? | Notes |
|---|---|---|---|---|
| Auth check (startup) | N-01 | splash/progress | full | readiness-gated |
| Login submit | N-03 | button spinner | button | disable double-submit |
| Initial list load (dashboard/work/reports/crew) | N-20/21/27/29 | skeleton list | content area | skeleton ≠ full screen |
| Pull-to-refresh | lists | top refresh spinner | list | content remains |
| Detail load (trip) | N-23 | skeleton/spinner | content | |
| Save new/edited work/trip | N-24/25 | submit spinner + pending | button/row | offline → "queued/syncing" label (not fake success) |
| Delete | N-20/22 | confirm→row progress | row | undo offered after |
| Attendance batch save | N-23/26 | per-op progress | rows | partial-failure handling |
| Search | N-20/27 | debounce + subtle | results | min length; clear |
| Pagination load-more | lists | footer spinner | footer | |
| Sync (offline→online) | background | "syncing" banner/indicator | none | WorkManager |
| Report aggregation | N-27/28 | skeleton/loading with progress | content | expensive via CF/scheduled |
| Export/report download | N-28/38 | progress + cancel | content | big data path |
| Backup/restore | N-38 | progress; disable UI | blocking on restore | reference did full-screen; keep but show steps |
| Image/photo upload | profile | per-image progress | field | Coil/cancel |
| Notification list | N-41 | skeleton | content | |
| Mark notification read | N-41 | per-item | row | offline queue read toggle (decide) |

## Principles
- Skeleton for content loads; button spinners for submits; footer spinner for pagination; refresh spinner for pull.
- Do not blank a populated screen to show a spinner on refresh (use stale + subtle refreshing) → also SuccessWithStaleData.
- Long operations (backup/export/aggregation) show progress + cancel and run off-main (WorkManager).
- Every loading state has a corresponding success/error/empty terminal (no stuck spinner).

## Gaps flagged
- Report aggregation & export are the heavy ops → must be off-main & cancellable (reference risk PERF-2).
- No indefinite "syncing" with no way to see pending items → show pending count/indicator (trust).
- Verification PROPOSED; loading states asserted in Compose UI tests.
