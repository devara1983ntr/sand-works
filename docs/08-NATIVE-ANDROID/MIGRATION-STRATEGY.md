# Migration Strategy (Flutter/Hive → Native/Firebase/Room)

Status: PROPOSED. **DECISION REQUIRED (D-3):** Whether any real production data exists in a deployed Flutter install. The native app is net-new; if no production Flutter data exists, there is **nothing to migrate** and that must be stated explicitly (never silently discard real data).

## 1. Premise
- The Flutter app is a single-user offline tool with Hive boxes and optional `.labourbackup` exports.
- The native app is a new, cloud-authoritative product. Most current single-user local data would, if imported, need to be assigned an owner/org and re-validated against new invariants (session numbering, catalogue references rather than free-text drivers).

## 2. Decision matrix
| Scenario | Handling |
|---|---|
| No production Flutter data (likely) | No migration required. State explicitly. Provide a one-time **import** path if an operator later wants to bring a `.labourbackup` into their org. |
| Operator has a `.labourbackup` | Optional importer: parse → validate → map Hive records to native (org-scoped) entities → human review of ambiguous fields (driver free-text → driver catalogue; trip numbering) → import with audit. |
| Operator still running Flutter while native launches | Keep Flutter as read-only legacy; no bidirectional sync in v1 (avoid split-brain). Provide export guidance. |

## 3. Import path design (if used)
```
.labourbackup (or Hive export) 
   → Validate (schema, bounds) 
   → Transform (map Work→WorkSession, Trip→Trip w/ driver catalogue resolution, Labour→Labourer, TripLabour→Attendance)
   → Review/confirm ambiguous mappings 
   → Import to org Firestore (via OWNER CF, idempotent, audited)
   → Verification report (counts, skipped)
```
Never silently discard data: report every skipped record and require confirmation.

## 4. What NOT to migrate
- Single-user/no-auth assumptions.
- Free-text driver names without a matching catalogue entry (require mapping).
- Draft autosaves (transient).
- Local-only backups as authoritative (import only on explicit user action).

## 5. Data format notes
Hive `.labourbackup` fields documented in the audit (`STORAGE-ANALYSIS.md`, `DATABASE.md`). Native export format is new (see `BACKUP-RECOVERY`/security): encrypted, signed, versioned.

## 6. Decision required (D-3)
Confirm existence of production Flutter data & whether import matters. Until then, treat migration as **optional importer (DEFER/off)**; no data-destructive actions.

## 7. Verification
PROPOSED. No migration code written (Phase 0).
