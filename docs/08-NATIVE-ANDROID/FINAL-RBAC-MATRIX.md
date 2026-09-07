# FINAL RBAC MATRIX — V1 (Phase 0.75)

Roles for V1 scope S-V1: **OWNER** (single active). DRIVER and LABORER appear as catalogue records, NOT access roles in V1 (D-1 deferred); ADMIN role reserved but inactive. Matrix below is authoritative for V1 (OWNER) and lists DRIVER/LABORER cells as **DEFERRED/DENY in V1** for honesty (V2 will re-populate from RBAC-Design).

Cell values: ALLOW / DENY / CONDITIONAL(note) / NA. Each row maps to: screen · use case · DB op · Firebase rule · test.

Operations keyed to FINAL-FEATURE-CATALOG.

| Operation | OWNER (V1) | ADMIN | DRIVER | LABORER | Screen | Use case | DB op | Rule | Test |
|---|---|---|---|---|---|---|---|---|---|
| Create WorkSession | ALLOW | reserved | DENY | DENY | N-24 | FV-10 | CF create | owner+org | FT-B1/FT-RULES |
| Read WorkSession/trip | ALLOW(org) | reserved | DENY(own deferred) | DENY | N-20/22/23 | FV-11..13 | read | owner | FT-B |
| Update trip (owner) | CONDITIONAL (session open; CF) | reserved | DENY | DENY | N-25 | FV-11 | CF | owner + status guard | FT-B2 |
| Record/Correct attendance | ALLOW (CF audit; reason for corrections) | reserved | DENY | DENY (view own deferred) | N-23 | FV-12 | CF | owner | FT-B3/FT-AUD |
| Delete (soft) trip/session | CONDITIONAL (owner, confirm, audit, CF) | reserved | DENY | DENY | N-20/22/23 | FV-15 | CF soft+cascade | owner | FT-B6 |
| Close session | ALLOW | reserved | DENY | DENY | N-22 | FV-16 | CF | owner | FT-B7 |
| Manage labourers | ALLOW(org) | reserved | DENY | DENY | N-29 | FV-20 | CF/owner | owner | FT-C1 |
| Manage drivers | ALLOW(org) | reserved | DENY | DENY | N-31 | FV-21 | CF/owner | owner | FT-C2 |
| Manage vehicles | ALLOW | reserved | DENY | DENY | N-30 | FV-22 | owner | owner | FT-C2 |
| Reports/analytics | ALLOW(org) | reserved | DENY | DENY | N-27 | FV-30 | read/CF agg | owner | FT-D |
| Export (CSV/backup) | ALLOW | reserved | DENY | DENY | N-27/38 | FV-31/40 | CF/storage | owner | FT-D2/FT-E |
| Backup/Restore | ALLOW (restore w/ confirm) | reserved | DENY | DENY | N-38 | FV-40/41 | CF | owner | FT-E |
| Profile edit (self) | ALLOW(self content) | — | — | — | N-40 | FV-50 | self update | self | FT-F1 |
| Account security | ALLOW(self; destructive re-auth) | reserved | DENY | DENY | N-39 | FV-51 | Auth/CF | self/owner | FT-F2 |
| Business settings | ALLOW | reserved | DENY | DENY | N-37 | FV-52 | CF | owner | FT-F3 |
| Audit viewer | ALLOW(read) | reserved | DENY | DENY | N-36 | FV-70 | read | owner | FT-H1 |
| Read auditLogs (raw) | ALLOW(owner) | DENY | DENY | DENY | N-36 | — | read | owner rule | FT-AUD |
| Change role/status | CONDITIONAL (CF only; ≥1 owner) | DENY | DENY | DENY | (CF) | — | CF | CF | FT-SEC |
| Account delete | CONDITIONAL (owner self-delete re-auth; cannot orphan org) | DENY | DENY | DENY | N-39 | FV-51 | CF | CF | FT-SEC |
| Send notifications | ALLOW(system/self) | reserved | DENY | DENY | N-41 | FV-60 | CF | CF | FT-G |

## V2 rows (deferred D-1/D-2/D-6) — recorded for continuity, NOT built now
Login/read own assigned for DRIVER, own attendance for LABORER, ADMIN delegation, driver status transitions, role-scoped notifications. Refer to RBAC-Design capability matrix when re-opened.

## UI == Rules == Backend agreement
- UI (Compose) shows OWNER actions only. A UI check is NEVER authorization.
- Firestore/Storage rules enforce every ALLOW/CONDITIONAL above (owner+org, self, parent scope).
- CF performs privileged ops (numbering, close, delete cascade, corrections audit, settings, backups, account/role).
- A mismatch between UI enablement and rules = a defect (IMPLEMENTATION-CONTRACT) and a rule/UI test.

## Guardrails
- No client can elevate role, change org, or write audit/status/deleted fields (SERVER-AUTHORITY-MATRIX). ≥1 active owner invariant enforced server-side. Single-owner V1 makes role cells trivial but the enforcement is still real (rules+CF), so V2 scales safely.
