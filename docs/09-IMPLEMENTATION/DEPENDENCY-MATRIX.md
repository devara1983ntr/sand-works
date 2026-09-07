# SAND WORKS — Dependency Matrix (task graph)

Status: **RECONCILED to `docs/10-SANDWORKS/`.** Supersedes retired S-V1 graph. Planning only.

Legend: `→` = prerequisite (must complete first). `‖` = independent/parallelisable. Blockers in bold are external to code and gate the row.

## Cross-phase dependency spine
```
Gate-0 (frozen spec)
   │
P1 Foundations ──► P2 Data & offline ──► P3 Auth/Security/Rules/CF ──► P4 Money ──► [P5 OWNER ‖ P6 DRIVER ‖ P7 LABOURER] ──► P8 Notifications ──► P9 Quality/Release
```

## Per-phase dependencies
**P1 (SW-101..108):** none in-plane (all depend on Gate-0). SW-101 (project) → SW-102..104 (DI/nav/DS/theme) → SW-105/106 (error/session/conflict) → SW-107/108 (money domain + a11y baseline), many ‖.

**P2 (SW-201..204):** ← P1. Chain: SW-201 (schema/outbox) → SW-202 (repos) → SW-203 (outbox+sync) ‖ SW-204 (concurrency).

**P3 (SW-301..309):** ← P2. Chain: SW-301 (Firebase bootstrap, **SW-BLK-1/2**) → SW-302 (auth) → SW-303/304 (provision/approval) → SW-305 (RBAC client) → SW-306 (rules) → SW-307 (CF B-01..18) → SW-308 (server-authority/audit) → SW-309 (attack tests). Rules (306) and CF (307) mutually inform; audit (308) consumes both.

**P4 (SW-401..404):** ← P2+P3. Chain: SW-401 (rate) → SW-402 (distribution) → SW-403 (closure, **SW-BLK-2 Blaze vs fallback**) → SW-404 (leaderboards). Money domain prebuilt in SW-108.

**P5 OWNER (SW-501..509):** ← P1..P4. Shell 501 → dashboards 502; 503 (approvals, needs CF/Gate-3), 504 (users/tractors), 505 (attendance), 506 (rates/rules settings, SW-401/402), 507 (settings), 508 (reports/export, **Blaze for cloud/storage**), 509 (audit viewer). Most ‖ after 501.

**P6 DRIVER (SW-601..605):** ← P1..P4. 601 shell → 602 dash → 603 add/edit trip (**CF numbering, Gate-3**; concurrency) → 604 share ‖ 605 profile(+Storage if Blaze).

**P7 LABOURER (SW-701..704):** ← P1..P4. 701 shell → 702 metrics → 703 history/leaderboard → 704 profile/notifications(needs P8 infra partially).

**P8 (SW-801..803):** ← P3 (CF), P4 (closure→earnings notification), P5-7 centres. 801 FCM (**SW-BLK-4**) → 802 centre/types A–F → 803 owner alert (needs SW-BLK-5 copy).

**P9 (SW-901..906):** ← everything. 901 test matrix → 902 a11y → 903 device/perf (**SW-BLK-6**) → 904 signing/release (**SW-BLK-3, 1**) → 905 security retest → 906 release/handover (**SW-BLK-5, A1/A2**).

## External / environment dependency register (not disguised as tasks)
| Blocker | Gates/phases it blocks | Category |
|---|---|---|
| SW-BLK-1 Firebase project+config | Gate-3, SW-301..309, release | CREDENTIAL/ENVIRONMENT |
| SW-BLK-2 Blaze vs Spark | SWF-24/25/26, cloud export/closure, SW-306/307/403/508/605/704/904 | BUSINESS DECISION |
| SW-BLK-3 signing keystore | release/private APK SW-904 | SECURITY/ENV |
| SW-BLK-4 FCM creds | Gate-8 SW-801..803, SWF-15/16 | CREDENTIAL/ENVIRONMENT |
| SW-BLK-5 notification/alert copy | SW-803 strings, Gate-8/9 | DESIGN/UX (approval) |
| SW-BLK-6 wireframes/UX lock | P5-7 final polish, SW-903 | DESIGN SPEC |
| SW-BLK-A1/A2 canonical assets | asset finalisation SW-906 | MISSING ASSET (confirm) |
