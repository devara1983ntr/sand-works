# System Flows

Status: Phase 0.5 (PROPOSED). Mermaid diagrams for architecture/data/state and user-action plumbing. Role-gated branches marked (D-1/D-6).

## 1. Global / auth navigation
```mermaid
flowchart LR
  Launch-->Splash[Splash N-01]
  Splash-->|no session|Login[Login N-03]
  Login-->|recover|Recover[Password N-04/05]
  Login-->Prov[Provision N-07 if no profile]
  Prov-->RoleResolve
  Login-->RoleResolve[Resolve role]
  RoleResolve-->O[Owner shell]
  RoleResolve-->D[Driver shell D1]
  RoleResolve-->L[Labourer shell D1]
  O-->OwnerNav[Owner destinations]
  D-->DriverNav
  L-->LabNav
  Disabled[Account disabled N-06]
  Expired[Session expired N-08]-->Login
```

## 2. Owner navigation
```mermaid
flowchart LR
  Home[Owner home N-20]-->New[New/Edit work N-24/25]
  Home-->Day[Day detail N-22]-->Trip[Trip detail N-23]
  Home-->Reports[Reports N-27]-->RepDet[Report detail N-28]
  Home-->More[More]
  More-->Crew[Crew N-29..32]-->User[Users/Roles N-33/34]
  More-->Notif[Notification N-41]-->Trip/Driver
  More-->Audit[Audit N-36]; More-->Ann[Announce N-35]; More-->Backup[Backup N-38]
  More-->Settings[Settings N-37]; More-->Profile[Profile N-40]; More-->Help[Help N-42]
```

## 3. Work lifecycle navigation
```mermaid
flowchart LR
  New[Create session N-24]-->Open[Open session]
  Open-->AddTrip[Add trip N-25]-->Trip[Trip detail N-23]
  Trip-->Attend[Record attendance]
  Trip-->Assign[Assign driver D6]
  Assign-->DriverWork[Driver N-51 confirm D1/D6]
  DriverWork-->Status[status update CF]
  Open-->Close[Close session W6]
  Trip-->Comp[Completed/Cancelled]
```

## 4. Error-recovery navigation
```mermaid
flowchart LR
  AnyOp[Any operation]-->|error|Err[Typed error state]
  Err-->|Retry idempotent|AnyOp
  Err-->|session expired|Reauth[Re-auth N-08]
  Err-->|forbidden|Forbidden[Forbidden state -> allowed area]
  Err-->|conflict|Conflict[Conflict UI -> merge/reload]
  Offline[Offline]-->Queue[Outbox queue]-->|reconnect|Sync[Sync -> reconcile]
```

## 5. Deep-link navigation
```mermaid
flowchart LR
  Notif[Notification/FCM deep link]-->Auth{Session}
  Auth-->|valid + role/owner|Target[Target screen N-23/51/38]
  Auth-->|expired|Reauth[Re-auth]-->Target
  Auth-->|role/not-found|Friendly[Friendly NotFound/Forbidden + list fallback]
```

## 6. UI→data architecture flow
```mermaid
flowchart LR
  UI[Compose UI/ViewModel]-->UseCase[UseCase]-->Repo[Repository]
  Repo-->Room[(Room local/outbox)]
  Repo-->Firebase[Firebase]
  Firebase-->Firestore[Firestore]
  Firestore-->Rules[Security Rules/Authz]
  Rules-->CF[Cloud Functions ops]
  CF-->Backend[(server state)]
  Rules-->Resp[Response/UiState]
  Resp-->UI
```
Reads: UI → Repo → Firestore cache(offline) live; writes: UI → UseCase → Repo → Room outbox(offline) or direct; server ops via CF idempotent.

## 7. User action → CF → Firestore → FCM → Recipient
```mermaid
flowchart LR
  Actor[Owner/Driver]-->Action[User action e.g. assign D6]
  Action-->BackendCall[Repository calls Cloud Function]
  BackendCall-->CF[CF validates+transacts]
  CF-->Write[(Firestore mutation)]
  Write-->Notify[FCM]
  Notify-->Recipient[Recipient device -> notification centre]
  Write-->Audit[(audit log)]
```

## 8. Master system diagram
```mermaid
flowchart TB
  User-->App[Native Android app]
  App-->Auth[Firebase Auth]
  App-->Authz[Security Rules]
  App-->Domain[Domain layer]
  Domain-->Repo[Repository]
  Repo-->FS[(Firestore)]
  Repo-->RT[(Room/DataStore cache+outbox)]
  Repo-->CF[Cloud Functions]
  CF-->FS
  FS-->CF
  Repo-->Storage[(Cloud Storage)]
  Repo-->FCM[Cloud Messaging]
  FCM-->App
  CF-->FCM
  App-->AppCheck[App Check]
  AppCheck-->Firebase
  Firebase-->Crash[Crashlytics]
  Firebase-->An[Analytics]
  Firebase-->RC[Remote Config opt]
  FS-->CF
  CFSched[Cloud Scheduler opt]-->CF
```

## Notes
- Every flow node must map to a screen + backend/data op (else gap flagged). Driver/labourer node groups gated D-1/D-6.
- The data-architecture flow and user-action flow must match the repo/VMM/UseCase layering in ANDROID-ARCHITECTURE.
