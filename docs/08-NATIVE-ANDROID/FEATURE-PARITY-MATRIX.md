# Feature Parity Matrix (current → native)

Purpose: ensure the native rebuild retains every current feature (or explicitly drops it). Status: current = VERIFIED; native column = PROPOSED. Commit audited `2dd2fe4`. Feature F-IDs from `01-PRODUCT/FEATURE-CATALOG.md`.

| F-ID | Current feature | Current status | Native Android equivalent | Firebase dependency | UX change | Migration risk | Priority |
|---|---|---|---|---|---|---|---|
| F-01 | Splash → dashboard | VERIFIED WORKING | Compose splash/session gate | none | minor | low | high |
| F-02 | Today dashboard summary | VERIFIED WORKING | Owner Home composable | optional | low | low | high |
| F-03 | Pull-to-refresh | VERIFIED WORKING | Compose pull-refresh on server lists | when online | low | low | medium |
| F-04 | Dashboard search | VERIFIED WORKING | Search on home/history | none/query | medium (extend to history) | low | medium |
| F-05 | Dashboard filter | PARTIAL (state-only) | Full filter bottom-sheet | none/query | high (implement) | low | medium |
| F-06 | Add Work+Trip form | VERIFIED WORKING | Job/Trip add/edit composable | store via Firestore | low | medium | high |
| F-07 | Next-trip quick add | VERIFIED WORKING | Copy-last-trip action | store | low | medium | high |
| F-08 | Auto trip numbering | VERIFIED WORKING | Domain usecase/server function | CF for server-authoritative numbering | none | medium | high |
| F-09 | Edit trip/work preserving id/date | VERIFIED WORKING | Edit composable + Room/Firestore upsert | yes | none | medium | high |
| F-10 | Labour master list | VERIFIED WORKING | Crew/Labourer list | users/crew doc | low | low | high |
| F-11 | Labour add/edit/remove in trip | VERIFIED WORKING | Attendance crew editor | yes | low | medium | high |
| F-12 | Attendance toggle | VERIFIED WORKING | Attendance toggles (role-aware) | attendance doc | low | medium | high |
| F-13 | Delete latest trip | VERIFIED WORKING | Remove trip (undo, role-gated) | yes | low | low | medium |
| F-14 | Delete specific trip (cascade) | VERIFIED WORKING | Cascade delete server-managed | yes | none | medium | high |
| F-15 | History grouped | VERIFIED WORKING | History list/paging | Firestore query | medium | low | medium |
| F-16 | Analytics KPIs | VERIFIED WORKING | Reports/KPI screens | aggregation (CF/scheduled) | medium | medium | medium |
| F-17 | Backup `.labourbackup` | VERIFIED WORKING | Signed/encrypted export + cloud backup | Storage | medium (auth) | medium | medium |
| F-18 | Restore w/ rollback | VERIFIED WORKING | Restore service (WorkManager) | Storage/CF | low | medium | medium |
| F-19 | Draft autosave | VERIFIED WORKING | Draft store (DataStore/Room) | none | low | low | high |
| F-20 | Theme | PARTIAL (dark-only) | Light/dark/dynamic tokenized theme | none | high (add light/dynamic) | low | medium |
| F-21 | About/Theme tiles | BROKEN/STUB (inert) | Real About + settings | none | high (implement) | low | low |
| F-22 | Details screen | PARTIAL/orphaned | Day-detail screen (reachable) or remove | yes | medium | low | medium |
| F-23 | Auth/accounts | MISSING | Firebase Auth | **yes** | high (new) | high | high |
| F-24 | Roles/RBAC | MISSING | Backend roles + rules | **yes** | high (new) | high | high |
| F-25 | Notifications | MISSING | FCM + CF role-scoped | **yes** | high (new) | medium | medium |
| F-26 | Cloud/backend/sync | MISSING | Firestore + offline reconcile | **yes** | high (new) | high | high |
| F-27 | Payments/reports service | MISSING | Reports first; payments only if justified | **yes** | high (new) | high | optional |
| F-28 | Accessibility | MISSING/partial | Built-in Compose semantics | none | high | low | high |
| F-29 | Localization | MISSING | strings.xml/plurals/RTL-ready | none | medium | low | medium |

## Decision callouts
- Core offline domain features (F-01..F-19) map cleanly → preserve; priority high.
- F-05/F-22 were partial → implement or remove intentionally in native.
- F-23..F-27 are net-new and drive the Firebase architecture; not present today.
- Offline-first must be preserved even as F-26 adds backend.
