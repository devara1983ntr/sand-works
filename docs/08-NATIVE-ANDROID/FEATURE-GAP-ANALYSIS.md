# Feature Gap Analysis

Status: Phase 0.5 (PROPOSED + VERIFIED). For every audited Flutter feature (F-IDs) and every proposed native feature, traces the chain Feature→Screen→Role→Behaviour and flags gaps. Feature completeness scorecard in PRODUCT-GAP-REGISTER.

## 1. Feature chain coverage
| Feature (ref F-ID) | Native screen(s) | Role | Gap found |
|---|---|---|---|
| F-01 Splash | N-01 | all | none |
| F-02 Today dashboard | N-20 | owner | none (adapt) |
| F-03 Pull-to-refresh | N-20 | owner | needs online refresh + offline guard |
| F-04 Search today | N-20 | owner | incomplete → full history search (see SEARCH) |
| F-05 Filter | N-20/N-27 | owner | Flutter filter unimplemented → define fully |
| F-06..09 Add work/trip/numbering/edit | N-24/N-25 | owner | numbering server-authoritative; edit preserves fields |
| F-10..12 Labour & attendance | N-23/N-26/N-31 | owner (+role) | attendance history/audit missing in reference |
| F-13..14 Delete trip | N-20/N-22 | owner | confirm+undo+audit+soft-delete |
| F-15 History | N-27/History | owner | history-wide search missing |
| F-16 Analytics | N-27 | owner | expensive aggregate → CF/scheduled |
| F-17..18 Backup/restore | N-38 | owner | encrypted/signed; cloud |
| F-19 Draft | N-24/N-25 | owner | keep |
| F-20 Theme | settings | all | light/dark/dynamic (was dark-only) |
| F-21 About/inert tiles | N-42/N-39 | all | implement or remove |
| F-23..27 Auth/Roles/Notifications/Backend/Reports | N-03..07, N-33..36, N-41, N-27 | role | net-new (see feature gaps below) |
| F-28 Accessibility | all | all | build into architecture |
| F-29 Localization | all | all | add string resources/plurals/RTL |

## 2. Net-new native feature gaps
| Gap feature | Required? | Reason/decision | Native screen |
|---|---|---|---|
| User account creation/invite | HIGH (owner) | multi-role needs users | N-33 |
| Role assignment & suspension | HIGH | RBAC | N-33/34 |
| Work assignment to driver | GATED D-1/D-6 | if driver self-service | N-50 |
| Driver accept/start/complete status | GATED D-6 | not in reference | N-51 |
| Labourer self-attendance | GATED D-1 | if self-service | N-60..63 |
| Notifications/FCM | later v1.x | product need (D-7/8) | N-41/N-35 |
| Announcements | later | owner→all | N-35 |
| Reports export | MED | needed by workflow | N-28 |
| Audit logs | MED/HIGH | privileged transparency | N-36 |
| Password reset/email verify | HIGH | auth lifecycle | N-04/05 |
| Search/filter/sort across history | MED | UX | N-20/N-27 |

## 3. Feature-level state-chain gaps (examples)
| Feature | Missing link found |
|---|---|
| Record attendance offline | Needs explicit "queued, syncing" UI + partial-failure handling (no fake success) |
| Reports | No export error/offline state defined |
| Delete trip | No cross-user conflict handling if another admin edits concurrently |
| Session numbering | Must be enforced server-side, not just client (avoid duplicates on concurrent adds) |

## 4. Verification
Reference features VERIFIED; native gaps PROPOSED/PARTIAL; role-gated features pending D-1/D-6/D-7.
