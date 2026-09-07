# API (current)

Status: VERIFIED. Commit `2dd2fe4`.

## Remote/backend API
**There is no remote API.** The product is fully offline (no `INTERNET` permission in `AndroidManifest.xml`, no `http`/`dio`/Firebase dependencies). `API.md` documents this explicitly so it is not mistaken for a missing integration.

- No REST/GraphQL/gRPC endpoints.
- No Firebase (Auth, Firestore, Storage, Functions, FCM) present — the future Firebase architecture is `PROPOSED` and specified in `08-NATIVE-ANDROID/`.
- No web services, no third-party SaaS calls.

## "API" surface that exists (local, in-process)
The application's business "interface layer" is the **domain repository contract** `WorkRepository` (`lib/features/work/domain/repositories/work_repository.dart`) implemented locally over Hive. This is the closest analog to an API and is documented in `API-SPECIFICATION.md`.

## Implication for native
The native app does **not** need to match any wire API today (there is none to match). A Firebase REST/Firestore surface must be newly designed (PROPOSED). Any claim that existing data flows over an HTTP contract is false.
