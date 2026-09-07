# ADR-008: Cloud Firestore as primary data store

## Context
Need a cloud store for org-scoped domain data that supports security rules, offline persistence, realtime-ish updates, and pagination — superseding Hive as the authoritative store.

## Decision
Use **Cloud Firestore** as the primary cloud application data store (conceptual schema in FIREBASE-DATABASE-DESIGN.md).

## Why chosen
- Rules-based authorization + offline persistence + flexible queries.
- Direct client access under well-tested rules (not CF-proxying every call).
- Live sync for owner/driver/labourer.

## Alternatives
- Realtime Database: less flexible queries, more complex permission modeling for this domain.
- Own backend DB: infra cost (ADR-007).
- Hive/Room only: no shared/secure cloud authority.

## Trade-offs / consequences
- Denormalization/composite indexes must be planned (documented).
- Query modeling matters; keep ownership keys (orgId) for rules.
- Security Rules are the enforcement boundary (ADR-010).
