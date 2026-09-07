# ADR-009: Authentication architecture

## Context
Product brief requires Owner/Admin/Driver/Labourer roles with server-authoritative authorization and **no hardcoded owner credentials** (owner = business identity Ramesh Sahu).

## Decision
Use **Firebase Authentication** (email/password for v1; phone evaluated only if D-1 adds driver/labourer field users). Owner/admin identities provisioned server-side (Cloud Function) and bound to role — never hardcoded. Session via Firebase token persistence; App Check attached.

## Why chosen
- Meets identity + server-authoritative-role requirement.
- Rules can check `request.auth.uid` and role.
- Password reset/email verification supported.

## Alternatives
- Custom auth: insecure, high cost, unnecessary.
- Hardcoded owner account: strictly forbidden.
- Enabling all providers: unnecessary attack surface.

## Trade-offs / consequences
- Choose only needed providers (email/password; phone conditional).
- Account status (active/suspended/deleted) enforced in rules/CF.
- Session-expiry/forbidden behaviour defined in FIREBASE-ARCHITECTURE.
