# QUERY / SECURITY-RULE COMPATIBILITY — V1 (Phase 0.75)

Mandatory rule: **Firestore Security Rules are not post-query filters.** A query's filters/ordering must satisfy the rules, or it fails/returns unauthorized results. Every client query below is rule-valid by design.

Format: Query · Purpose · Actor · Filters · Ordering · Pagination · Required indexes · Rule compatibility · Potential unauthorized result.

## Query catalogue
| Q | Purpose | Actor | Filters | Ordering | Pagination | Required indexes | Rule compatibility |
|---|---|---|---|---|---|---|---|
| Q-1 session by (date,session) | dashboard today / uniqueness | OWNER | `orgId==mine && date==d && session==s` | date | none | `orgId+date+session` | Rules require `orgId==get.owner` so filter matches rule predicate — legal |
| Q-2 workSessions by date | day/session list | OWNER | `orgId==mine && date==d && deletedAt==null` | date | — | `orgId+date+deletedAt` | predicate on deletedAt is rule-valid (rules only allow reading undeleted) |
| Q-3 trips by session | trip list | OWNER | `orgId==mine && deletedAt==null` | tripNumber | cursor on tripNumber | composite `orgId+deletedAt+tripNumber` | same-field rule predicate; legal |
| Q-4 trip by number | verify | OWNER | `sessionId==s && tripNumber==n` | — | — | `sessionId+tripNumber` | within session collection scoped by parent rule |
| Q-5 attendance by trip | N-23 | OWNER | trip sub | labourerId | — | — | subcollection rule; owner-of-org |
| Q-6 labourers/drivers list | pickers | OWNER | `orgId==mine && active==true` | name | cursor | `orgId+active` | rule requires orgId==owner + active check consistent |
| Q-7 history search | full-history | OWNER | orgId + optional text | date desc | cursor | search via app-side index or simple fields (avoid free-text Firestore) | must not query free-text beyond rules → design text search client-side over cached/denormalised set |
| Q-8 notifications for me | N-41 | self(owner) | `userId==uid` | createdAt desc | cursor | `userId+read+createdAt` | rule `userId==request.auth.uid` matches filter |
| Q-9 audit list | N-36 | OWNER | `orgId==mine` | createdAt desc | cursor | `orgId+createdAt` | rule owner read |
| Q-10 "my pending queued" (Room local) | offline | OWNER | local | created | — | Room index | local, no rule |
| Q-11 top driver/aggregates | analytics | OWNER | orgId | derived | — | use cached/CF counters; not a live cross-doc query | keep to owner scope |
| Q-12 driver history / labourer history | V2 D-1 | — | — | — | — | add `labourerId+date` index when enabled | gated D-1 |

## Rule-validity requirements
1. Every query filter MUST include a term the rules evaluate on the same document field (orgId/ownerId, userId for self, session parent). No query relying on client-side post-filtering of rule-denied docs.
2. Ordering fields MUST be indexed and within rule-allowed doc set.
3. Free-text search is NOT done as a Firestore `array-contains`/`>=` over labourer names beyond owner scope; keep simple equality filters or an app-maintained denormalised search index owned by the owner. Do not over-query.
4. deletedAt: rules expose only undeleted docs to reads (`deletedAt == null`), and queries include it — no rule-returned "deleted" rows to filter client-side.
5. Subcollection rules inherit parent auth (session/trip owner).

## Potential unauthorized results (must never happen)
- Any query returning another org's records → prevented because `orgId==mine` + rule equality.
- Any query returning deleted records → prevented by deletedAt==null both in query and rule.
- A labourer query returning other users → V1 owner-only; V2 uses `labourerId==uid`.

## Index management
- Composite indexes required for: Q-1, Q-2, Q-3, Q-6, Q-8, Q-9 (+Q-4 subcollection). Managed in `firestore.indexes.json`. Every index maps to ≥1 query test in the emulator (FINAL-TEST-CONTRACT). If an index is missing, the query fails at runtime → each required index is declared now, not discovered late.

## Test
Emulator rule tests run every Q with (a) authorized owner → success, (b) wrong-org/unauth/deleted-filtered → deny, proving rule-validity (FT-RULES).
