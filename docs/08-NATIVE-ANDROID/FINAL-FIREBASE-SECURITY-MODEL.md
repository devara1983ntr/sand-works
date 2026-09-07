# FINAL FIREBASE SECURITY MODEL — V1 (Phase 0.75)

Auth · role/claims · Firestore rules · Storage rules · App Check · CF authorization · ownership · org scoping · audit · disabling · deletion · abuse protection. Rule-of-three everywhere (UI + Rules + CF). No security logic exclusively in Compose; not every DB op behind CF.

## 1. Authentication (Firebase Auth)
- Email/password for the single OWNER account. Password reset. Optional 2FA deferred (non-blocking). Email verified before owner provisioning (B-01).
- Single owner; no self sign-up to a role (provisioning via owner-initiated token/secret — DECISION: default allowlist token).

## 2. Role strategy
- Custom claim `role` minted at provisioning and on CF role changes + `users.role` as canonical. V1: only OWNER active. Rules read role via claim (fast) with `users.status` active check each op; disable = revoke claim + set status + reject. Client never writes role/status.
- Claim refresh on disable; local cache cleared on disable/delete.

## 3. Firestore rules (per collection — summary; authoritative with implementation + emulator tests)
- Helper: `isOwner(userId) = exists(/users/$(uid)) && get(/users/$(uid)).data.role=='OWNER' && status=='active' && orgId==<doc.orgId or owner.org>`; `belongsOrg(doc)` compare doc.orgId to owner org.
- orgId equality drives org scoping; deletedAt==null on reads (rules are not filters → queries include deletedAt==null and match).
- clients may not write: role, status, ownerUid, orgId, createdAt/updatedAt (server ts), createdBy/changedBy, tripNumber, rev beyond expected, deletedAt/by, audit fields, isPresent derived, counters, approval.
- auditLogs/attendanceHistory: client write+delete DENY; read owner.
- notifications: read/update read by self only; create CF only.
- server-only fields validation via `request.resource.data` guards.

## 4. Storage rules
- `backups/{orgId}/{file}`: read/write OWNER of that org (signed URL for export/restore w/ expiry). `avatars/{uid}` self read/write. `exports/{orgId}/...` owner. **No public/** read; no anonymous. Enforce size/content-type.

## 5. App Check
- Enforced on Firestore, Storage, Functions (Play Integrity on Android) → blocks non-verified clients (AT-14). Emulator test path for dev.

## 6. Cloud Functions authorization
- CF validates `context.auth.uid`, role claim, org, input; performs the privileged/transactional op and writes audit. CF functions themselves enforce owner+org; a CF is never a thin unauthenticated relay.

## 7. Ownership & org scoping
- Every business doc has orgId; owner ops constrained to own org. users scoped by uid + owner. Denormalised owner only to owner org.

## 8. Audit
- Server-generated only (AUDIT-LOG-INTEGRITY), append-mostly, owner-read, no client write/delete.

## 9. Account disabling / deletion
- Disable: CF revokes claim + status=suspended + rules deny; notify; clear client cache. Delete: CF re-auth+guards (≥1 owner), anonymize labourer/driver/org refs or deactivate org, audit; local cache cleared. Never hard-delete org while records exist without governed purge (D-8).

## 10. Abuse / rate limiting / monitoring
- Auth rate limits; Functions quotas; CF retry with backoff + opId dedupe (no double side effects); Cloud Monitoring alerts on error rate, quota, unexpected FCM/storage egress; Crashlytics. No unbounded client queries (indexes only).

## Function boundary (decision, per §30)
| Concern | Location |
|---|---|
| UI render/layout/state | Android (Compose) |
| Input UX + client pre-validation + offline queue | Android |
| Simple per-field/ownership authorization + read scoping + immutable-field denial | Firestore/Storage Security Rules |
| Trusted privileged/business operations (numbering, uniqueness txn, close, delete-cascade, corrections+audit, settings, account/role, backups/export/restore, notifications) | Cloud Functions |
| Do NOT: put security only in Compose; wrap every read in a CF; trust client role/status. |
| Reads/queries are direct Firestore subject to Rules (no per-read CF). |

## Tests
FT-SEC/FT-RULES/FT-AUD/FT-OFFLINE cover every rule cell, escalation, ownership, disable/delete, storage rules, App Check enforcement, CF authz + idempotency (FINAL-TEST-CONTRACT).
