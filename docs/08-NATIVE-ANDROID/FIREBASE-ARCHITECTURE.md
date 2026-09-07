# Firebase Architecture

Status: PROPOSED (Phase 0). This is the conceptual architecture; **no Firebase project/config is created** (D-4). Each service below states its responsibility and why (or why not) it is used.

## 1. Service responsibilities
| Service | Responsibility | Decision | Why |
|---|---|---|---|
| Firebase Authentication | Identity + session lifecycle | ✅ Use | email/password (owner/admin + any staff); phone evaluated for driver/labourer field users if self-service (D-1). See Authentication section. |
| Cloud Firestore | Primary cloud application data (authoritative) | ✅ Use | org-scoped domain data, offline persistence, security rules |
| Cloud Storage | Only binary/file data needing cloud | ⚖ Conditional | profile images, media, encrypted backup exports. Not needed if we defer photos |
| Cloud Functions | Trusted server-side ops only | ✅ Use (selectively) | role assignment/transfer, audit writes, authoritative trip numbering, notifications, destructive ops, backup orchestration. **Not** a wrapper around every Firestore call — direct client+Rules is preferred |
| FCM | Push notifications | ✅ Use (Phase later; enable in v1.x) | role-scoped assignment/attendance/admin notices |
| App Check | App authenticity / abuse protection | ✅ Use | protect Firestore/Storage/Functions from non-app clients |
| Crashlytics | Crash monitoring | ✅ Use | release quality |
| Analytics | Product analytics where justified | ⚖ Optional/opt-in | only if product value confirmed; privacy-reviewed, non-PII |
| Remote Config | Legit remote config only | ⚖ Optional | e.g., feature flags/thresholds if operationally valuable; do not add for show |

## 2. Authentication architecture (identity → session → role)
```
Unauthenticated
   └─> Email/Password (or phone) sign-in
        └─> Identity established (auth.uid)
             └─> Account status check (active/suspended/deleted)
                  └─> Role resolution (users.role + custom claim)
                       └─> Authorized application (role-driven UI, server-gated data)
```
Behaviour matrix:
| Condition | Behaviour |
|---|---|
| Invalid credentials | Auth error → clear message; no account enumeration |
| Disabled account | Status `suspended`/`disabled` → reject sign-in or restrict; message; support path |
| Deleted account | Auth error "no account"; offer create/recover |
| Network failure / Firebase outage | If offline & authenticated session valid → continue on local cache; if sign-in required & offline → message + retry (offline sign-in only if we adopt Firebase token persistence + cached rules decision; see OFFLINE-SYNC) |
| Expired/revoked session | Force re-auth → sign-in; preserve offline queue; no data loss |
| Unauthorized role | After auth, rules deny → Forbidden state; route to minimal profile/home; never silently grant |
| Incomplete profile | On first login, if profile missing (orgId/role not yet linked) → provisioning/onboarding screen |

Decisions:
- Enable **email/password** only for v1 unless D-1 adds phone. Do NOT auto-enable every provider (Google/Apple/Facebook not required).
- **Password reset**: Firebase reset email — owner/admin + staff accounts.
- **Email verification**: recommended for admin/owner identity.
- **Account activation**: provisioning link/CF creates account with temp setup; **no hardcoded owner credentials**.
- **Session**: Firebase token persistence; App Check attached; token refresh handled by SDK.
- See ADR-009, `ANDROID-SECURITY-ARCHITECTURE.md`, `RBAC-DESIGN.md`.

## 3. Service boundary diagram
```mermaid
flowchart LR
  Owner/Driver/Lab [Client App] --> AppCheck[App Check]
  AppCheck --> Auth[Firebase Auth]
  Auth --> FS[Firestore rules]
  Auth --> ST[Storage rules]
  CF[Cloud Functions] --> FS
  CF --> Push[FCM]
  CF --> Audit[(audit_logs)]
  FS --> Crash[Crashlytics]
```

## 4. Explicit anti-patterns
- Cloud Functions are NOT a generic proxy for Firestore. Direct client reads/writes under well-tested Security Rules are the default; Functions only where trusted logic/rules can't express it.
- No Firebase service-account/admin credentials ever placed in the Android app.

## 5. Ownership/environment
No actual project IDs invented (D-4). Environment strategy in `CI-CD-ARCHITECTURE.md`.
