# Business Rule Matrix

Status: Phase 0.5 (PROPOSED). Consolidates and extends the audit BR register (BR-*) with authorization ("Who may…"), plus proposed native rules. Evidence-based; where no evidence exists the rule is PROPOSED/REQUIRES DECISION.

Legend per row: Owner/Admin, Driver, Labourer (ALLOW/DENY/CONDITIONAL/N-A). Enforcement: R=Firestore rules, CF=Cloud Function.

| Business rule | Enforcement | Owner/Admin | Driver | Labourer |
|---|---|---|---|---|
| Create a WorkSession (unique date+session in org) | R/CF (uniqueness) | ALLOW | DENY | DENY |
| Assign driver/labourer to a trip | R | ALLOW | DENY | DENY |
| Record attendance for a labourer on a trip | R/CF (owner) | ALLOW | DENY (unless D-1 confirm via CF) | DENY (modify others) |
| Read assigned work | R (scope) | ALLOW (all org) | ALLOW (own assigned) | read own attendance only |
| Update trip/work status | CF validates state | ALLOW | CONDITIONAL (own, via CF, if D-6) | DENY |
| Edit completed work | CF | CONDITIONAL (owner, restricted+audit) | DENY | DENY |
| Delete records (trip/session) | CF soft-delete | CONDITIONAL (owner, audit) | DENY | DENY |
| View another user's records | R | ALLOW (org, admin) | DENY | DENY |
| Change role | CF (owner only) | ALLOW(owner) | DENY | DENY |
| Suspend/activate user | CF (owner) | ALLOW(owner) | DENY | DENY |
| Read reports | R/CF | ALLOW | CONDITIONAL (own summary) | DENY |
| Send announcement/notification | CF | ALLOW(owner) | DENY | DENY |
| Export/backup data | CF/Storage | ALLOW(owner) | DENY | DENY |
| View audit logs | R | ALLOW(owner/admin) | DENY | DENY |

## Domain invariants (carried from BR register, server-enforced)
- BR-1/2: WorkSession unique (org,date,session); Trip belongs to one session.
- BR-4/5: trip numbering sequential & server-authoritative; new date/session resets.
- BR-6: labourer names persist exactly as typed.
- BR-7: cascade on trip delete → attendance soft-delete.
- BR-8: removing attendance link does not delete labourer master.
- BR-12: removed-labour → attendance present=false (soft), not delete (preserve history).
- BR-14: restore all-or-nothing w/ snapshot+rollback+verification.
- BR-18 (refactor): define a real trip status lifecycle (D-6) — reference had only a default string.
- NEW: attendance is versioned/auditable (reference lost history).
- NEW: session/date boundary value fixed (D-5).

## Rules with no direct evidence → PROPOSED/DECISION
| Rule | Basis | Decision |
|---|---|---|
| Which roles get self-service login | no evidence | D-1 |
| Driver status workflow scope | no evidence | D-6 |
| Admin delegation beyond owner | no evidence | D-2 |
| Attendance confirm by labourer | no evidence | D-1 |

## Verification
Carried rules VERIFIED from BR register; authorization mapping PROPOSED; server enforcement mandatory (client-side checks insufficient). Emulator tests in ANDROID-TESTING-ARCHITECTURE.
