# Authentication & Authorization

Status: current (VERIFIED: none) + native design (PROPOSED). Commit `2dd2fe4`.

## 1. Current state
- **Authentication: none.** No login; app opens directly to the dashboard.
- **Authorization: none.** All screens/actions available to the single local user.
- **Owner identity:** The brief's owner **"Ramesh Sahu"** is not represented anywhere in code (no hardcoded owner account — which is correct practice, but also no auth at all). Package id uses "roshan".
- Implication: nothing currently prevents anyone holding the device from reading/writing all records. Acceptable only while the product remains strictly single-user/single-device.

## 2. Native authentication design (PROPOSED)
Recommended: **Firebase Authentication** with an email/password primary path (and phone as an evaluated option given operator context). Requirements:
- Session lifecycle: Firebase token refresh handled by SDK; sign-out explicit; session expiry handled gracefully (route to login with a message; no data loss of local offline queue).
- Account recovery: standard Firebase password reset flow (evaluated).
- **Owner/admin must never be hardcoded.** Owner UID bound to a role server-side at provisioning.

## 3. Native authorization design (PROPOSED)
- Represent roles **backend-authoritatively**. Evaluate two complementary mechanisms:
  1. **Custom claims** on the auth token (fast, cached server-side, ideal for coarse role gating used in Firestore rules via `request.auth.token.role`).
  2. **Role document / profiles** in Firestore for richer role metadata + audit of role changes.
- A **Cloud Function (admin-only)** assigns roles at provisioning and on change; the client can never self-assign.
- Firestore **security rules** enforce reads/writes by `request.auth.uid`, role, and ownership. Cloud Functions perform privileged/transactional operations that rules cannot express.

> «Client-side role checks are NOT sufficient authorization.» In the UI, role determines *visibility/routing only*; every sensitive operation is re-checked server-side.

## 4. Session & token storage (native, PROPOSED)
- Firebase persists tokens via SDK securely; Android Keystore for any app-managed secrets.
- Do not log tokens; do not store tokens in plaintext prefs/DataStore.

## 5. Re-auth for sensitive ops
Require recent login (Firebase `reauthenticate`) for destructive admin actions (delete account, role downgrade, wipe) — PROPOSED.

## 6. Current gaps to close in native
- No account at all → add auth.
- No role model → add backend roles + RBAC.
- No owner representation → add provisioning-time server role.
