# Firebase Security Rules (recommended)

Status: PROPOSED (rules pseudocode/design only; no Firebase in current repo). Commit audited `2dd2fe4`.

## 1. Principles
- Default-deny. Every rule checks `request.auth != null`, role, and ownership.
- Client role flags never authorize; rules/CF re-check using `request.auth.token.role` and user/role docs.
- Use custom claims for coarse role checks in rules; role documents for richer metadata.
- Cloud Functions for operations rules cannot express (transactions, aggregation, role assignment, notifications).

## 2. Helper functions (pseudocode)
```
function signedIn() { return request.auth != null; }
function isOwner(orgId) {
  return request.auth.token.org == orgId && request.auth.token.role in ['owner','admin'];
}
function isMember(orgId) { return request.auth.token.org == orgId; }
```

## 3. Firestore rules sketch
```
rules_version = '2';
service cloud.firestore {
  match /databases/{db}/documents {
    function signedIn() { return request.auth != null; }
    function role() { return request.auth.token.role; }
    function orgOf() { return request.auth.token.org; }

    // profiles: users may read self; owner may read org users
    match /users/{uid} {
      allow read: if signedIn() && (uid == request.auth.uid || role() in ['owner','admin']);
      allow create: if signedIn() && uid == request.auth.uid;
      allow update: if signedIn() && uid == request.auth.uid
                    && request.resource.data.diff(resource.data).affectedKeys().hasOnly(['phone','displayName','profileImage']);
      // role/status changes ONLY via Cloud Function (admin)
    }

    // jobs
    match /jobs/{jobId} {
      allow read: if signedIn() && isMember(resource.data.orgId)
                  && (role() in ['owner','admin','manager']
                      || resource.data.assignedDriverId == request.auth.uid);
      allow create: if signedIn() && role() in ['owner','admin']
                    && request.resource.data.orgId == orgOf();
      allow update: if signedIn() && role() in ['owner','admin'] && orgOf()==request.resource.data.orgId;
      // driver may only transition own job status via Cloud Function (not direct write)
      // nested trips/attendance similar with org + ownership checks
    }

    // auditLog — server only
    match /auditLog/{id} {
      allow read: if signedIn() && role() in ['owner','admin'];
      allow write: if false;   // only Cloud Functions (admin SDK bypasses rules)
    }
  }
}
```

## 4. Storage rules sketch
```
service firebase.storage {
  match /b/{bucket}/o {
    match /orgs/{orgId}/profile/{uid}/{file} {
      allow read: if signedIn() && member(orgId);
      allow write: if signedIn() && (uid == request.auth.uid && contentType within allowlist)
                   || role in owner/admin;
    }
    match /backups/{orgId}/{file} {
      allow read,write: if signedIn() && role() in ['owner','admin'] && orgOf()==orgId;
    }
  }
}
```

## 5. Cloud Functions authorization rules
- Role assignment & change: admin-only function; verifies caller is owner/admin via admin SDK; writes profile + updates custom claims; audit logs.
- Job assignment / status transitions: validates role & state machine; writes atomically; notifies via FCM; audit logs.
- Notifications & scheduled jobs: CF only.
- Backup creation/restore: CF (owner) writes to Storage with App Check + auth.

## 6. App Check
- Enforce App Check on Firestore/Storage/Functions so only the genuine app reaches resources; handle Play integrity/attestation provider per platform.

## 7. Replay / abuse / rate limiting
- Cloud Functions enforce quotas; idempotency keys for retried operations; alert on anomaly.

## 8. Testing the rules
- Use the Firebase emulator + rules unit tests in CI to validate role matrix (owner/driver/labourer × read/write per collection).

## 9. Do-not
- Never write rules allowing `allow write: if true`, broad `get('/users/..')` without role checks, or client-side role trust.
