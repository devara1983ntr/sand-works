# ADR-011: Local persistence strategy

## Context
Flutter used Hive as the offline-authoritative store. Native must decide what local relational/persistent storage is justified (Room is not automatic).

## Decision
- **Room** only where local relational persistence is genuinely justified: the **offline write outbox/queue** and the **local cache of Firestore data** that benefits from relational/transactional handling. If the only local need is a simple Firestore cache + outbox, Room is used narrowly for those tables.
- **DataStore** for preferences (non-sensitive) and cached feature flags.
- **Encrypted storage** (SQLCipher/Keystore-backed) for any sensitive cached PII where justified (D-8).
- Draft autosave for owner data entry lives locally (Room/DataStore) — KEEP from reference.

## Why chosen
- Avoids Room when unneeded (no heavy local relational domain); Firestore is authoritative.
- Outbox needs transactional local writes → Room justified there.
- Encrypt only what is actually sensitive.

## Alternatives
- Hive: offline-authoritative single-user, not appropriate for shared cloud data.
- Room for everything: over-engineering; duplicates Firestore.
- DataStore for everything: not suited for relational outbox.

## Trade-offs / consequences
- Narrow Room usage; encryption decision pending D-8 privacy.
- Local = cache + outbox, never sole source of truth (except drafts).
