# ADR-007: Firebase as Backend Platform

## Context
The Flutter app was fully offline single-user. The product brief requires secure multi-role operation with a backend. Decision is which backend platform.

## Decision
Use **Firebase** (Authentication, Firestore, optional Storage, Cloud Functions, FCM, App Check, Crashlytics) as the managed backend platform.

## Why chosen
- Managed, serverless; rapid secure app auth + rules + offline persistence.
- Aligns with requested stack and Google guidance (Auth + Security Rules for mobile authorization, App Check).
- Enables offline-capable Firestore + FCM + Cloud Functions without infra.

## Alternatives
- Custom REST backend + own DB: high infra/ops cost, slower to market, more security surface.
- Supabase/other BaaS: viable but diverges from the mandated Firebase stack.

## Trade-offs / consequences
- Vendor lock-in to Firebase.
- Requires strong Security Rules + App Check + CF discipline (ADR-008..010).
- Remediate audit SEC-1 before any release. Env strategy in CI-CD-ARCHITECTURE.
