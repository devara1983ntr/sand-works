# RBAC Design

Status: PROPOSED (Phase 0). Enforcement model: client renders by role; **authorization is enforced in Firestore Security Rules + Cloud Functions** (`FIREBASE-SECURITY-RULES-DESIGN.md`). Cell values: ALLOW / DENY / CONDITIONAL / NOT APPLICABLE. D-1 (labourer login scope) pending — matrix assumes all roles exist.

## 1. Roles
| Role | Meaning |
|---|---|
| OWNER | Business owner (Ramesh Sahu) — highest privilege, business identity, provisioned server-side, never hardcoded |
| ADMIN | Delegated operator within the owner's org (manages day ops) |
| DRIVER | Tractor/driver operator (performs trips) |
| LABORER | Field worker (attendance subject) |

ADMIN optional in v1 (D-2 scope); OWNER and ADMIN share an admin capability set where ADMIN is a scoped delegate.

## 2. Capability matrix
Legend: ✅ ALLOW · 🚫 DENY · ⚖ CONDITIONAL (condition noted) · ➖ NOT APPLICABLE

| Capability | OWNER/ADMIN | DRIVER | LABORER |
|---|---|---|---|
| Login | ✅ | ✅ | ✅ (requires account; D-1) |
| View own profile | ✅ | ✅ | ✅ |
| Edit own profile (own name/phone/photo) | ✅ (not role/email) | ✅ (not role/email) | ✅ (not role/email) |
| View dashboard | ✅ | ⚖ driver dashboard (own assigned trips) | ⚖ labourer summary (own attendance) if self-service |
| Manage users/accounts | ✅ (OWNER) ; ADMIN ⚖ if delegated | 🚫 | 🚫 |
| Manage drivers (catalogue) | ✅ (OWNER); ADMIN ⚖ | 🚫 | 🚫 |
| Manage labourers (catalogue) | ✅ (OWNER); ADMIN ⚖ | 🚫 | 🚫 |
| Create work session | ✅ | 🚫 | 🚫 |
| Assign work/labour/driver to trips | ✅ | 🚫 | 🚫 |
| View assigned work | ✅ all | ✅ own (CONDITIONAL by driverId) | ✅ own attendance (if self-service) |
| Update work/trip status | ✅ | ⚖ confirm/completion of own assigned trip only (if driver workflow) | 🚫 |
| Record/confirm attendance | ✅ (record) | ⚖ confirm own trip attendance? (recommend: DENY direct; owner/CF records) | ✅ view own; 🚫 modify others |
| Edit completed work | ⚖ (owner only, audited, restricted window) | 🚫 | 🚫 |
| Delete records | ⚖ (owner; audited; soft-delete) | 🚫 | 🚫 |
| View reports | ✅ | ⚖ own-trip summary only | 🚫 (own attendance only) |
| Manage notifications | ✅ (owner sends) | receive own | receive own |
| Notification preferences | ✅ | ✅ | ✅ (if account) |
| System/business settings | ✅ (OWNER) | 🚫 | 🚫 |
| Audit logs | ✅ (OWNER/ADMIN read) | 🚫 | 🚫 |
| Role changes / suspend users | ✅ (OWNER; CF) | 🚫 | 🚫 |
| Account deletion | ⚖ own account self-delete (with safeguards); owner deletion protected | ⚖ own | ⚖ own |
| Data export | ✅ (owner) | 🚫 | 🚫 (own-data request) |

## 3. Rule of three for enforcement
1. UI shows actions the role may take (routing/visibility).
2. Firestore/Storage **security rules** deny/allow every read/write by `request.auth.uid`, role, and ownership/org.
3. **Cloud Functions** perform operations the client must not do directly (role assignment, audit writes, notifications, authoritative numbering, destructive deletes).

## 4. Ownership primitives used in rules
- `orgId` on every business record.
- `assignedDriverUid` (or driver link) to scope driver reads/writes.
- `labourerUid` to scope labourer reads.
- `createdBy`/`changedBy` = `request.auth.uid`.
- Role stored in `users.role` (authoritative; rule checks `request.auth.uid` → `get users doc` or custom claim) — see ADR-010.

## 5. Anti-patterns (explicit)
- No `allow read, write: if request.auth != null;` as a whole-collection strategy.
- No trusting client `isAdmin`, `role`, `permissions`, `owner` fields as authorization.
- No unconditional cross-user reads/writes.

## 6. Open decisions
D-1 (driver/labourer self-login), D-2 (ADMIN delegation), D-6 (driver job-status workflow). Until resolved, security rules grant the most restrictive safe set.
