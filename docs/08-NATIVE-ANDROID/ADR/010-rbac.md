# ADR-010: RBAC model

## Context
Product requires distinct capabilities per role; authorization must be backend-enforced (never client-side `isAdmin`).

## Decision
Model roles as OWNER, ADMIN, DRIVER, LABORER. Store authoritative role in `users.role` (and mirror in a custom claim for rules efficiency). Enforce in Firestore/Storage Security Rules + Cloud Functions. Client uses role only for routing/visibility. RBAC matrix in RBAC-DESIGN.md.

## Why chosen
- Backend-authoritative role satisfies the security requirement.
- Custom claims + rules give coarse role checks; `users` doc adds richer metadata/audit.
- Role assignment/transfer only via admin Cloud Function (prevents self-escalation).

## Alternatives
- Client-side role flag only: insecure, forbidden.
- Rule checks only on `users` doc each time: more reads; combine with claims.

## Trade-offs / consequences
- Must keep custom claims + `users.role` in sync (CF does both, audited).
- Ownership keys (orgId, assignee, labourerId) drive all rules.
- Emulator tests required for every role×verb.
