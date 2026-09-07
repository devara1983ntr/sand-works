<div align="center">

# 🏗️ Sand Works — Labour & Tractor-Trip Management

**Offline-first field operations · Multi-role architecture · Production-grade engineering blueprint**

The complete, audit-grade repository for **Sand Works**, a labour-contractor / transport-operator platform that digitises a working day of **Work → Trips → Labour attendance** (sand, soil & stone haulage by tractor). It ships as a working offline-first **Flutter reference app** plus the full **native Android** rebuild specification — architecture, security, database, backend and a 52-task implementation-control system — so a separate engineering team can build it without guessing.

[![Kotlin](https://img.shields.io/badge/Kotlin-Compose%2FM3-7F52FF?logo=kotlin&logoColor=white)](#) [![Architecture](https://img.shields.io/badge/Status-Specification%20%2B%20Control%20Plan-blueviolet)](#) [![Firebase](https://img.shields.io/badge/Firebase-Auth%20%2F%20Firestore%20%2F%20Functions%20%2F%20FCM-FFCA28?logo=firebase)](#) [![License](https://img.shields.io/badge/License-All%20Rights%20Reserved-inactive)](#)

</div>

---

## ✨ What this repository is

| Layer | Contents |
|---|---|
| **Reference App** | Existing offline-first **Flutter** implementation (the behavioural source of truth under `lib/`). |
| **Forensic Audit** | Full product/UX/engineering/security/performance audit of the reference app (`docs/01`–`docs/07`). |
| **Native Blueprint** | Kotlin + Jetpack Compose + Material 3 architecture, ADRs, RBAC, Firebase design (`docs/08-NATIVE-ANDROID`). |
| **Final Design Closure** | Product freeze, feature/screen catalogs, business rules, state machines, schema, security model — decisions resolved or explicitly blocked (`08-NATIVE-ANDROID/FINAL-*`). |
| **Implementation Control** | Dependency-aware, gate-controlled, 52-task execution plan (`docs/09-IMPLEMENTATION`). |

---

## 🎯 Core domain

- **Work Session** — one day's operation, scoped by date + Morning/Evening session.
- **Trips** — sequential tractor trips with driver & vehicle, auto-numbered per day.
- **Labour & Attendance** — per-trip present/absent with an **append-immutable, audited correction trail**.
- **History, Analytics & Search** — grouped history, KPIs and a sortable data table.
- **Backup / Restore** — portable, encrypted & signed `.labourbackup` files.

---

## 🏛️ Native architecture highlights

- **Clean Architecture** — Presentation (Compose UDF) → Domain ← Data, Hilt DI, single-activity Navigation Compose.
- **Offline-first & honest** — Room cache + deterministic outbox + WorkManager sync. **No fake success**: a queued write is never shown as saved.
- **Server-authoritative security** — role, org, timestamps, audit, numbering & status are enforced in **Firestore/Storage Security Rules + Cloud Functions**, never trusted from the client.
- **Integrity-first** — optimistic concurrency (`rev`), idempotency keys, Firestore transactions, CF-generated audit logs.
- **Quality gates** — accessibility, responsive (M3 adaptive), performance & release contracts, plus a strict **Zero-Placeholder / Zero-Fabrication** engineering policy.

> 📖 Start reading at **`docs/README_INDEX.md`** and **`docs/00-AUDIT-INDEX.md`**. The binding coding rules live in **`docs/03-ENGINEERING/AGENT.md`**.

---

## 🗂️ Documentation map

```
docs/
├── 01-PRODUCT            product requirements, rules, personas, PRDs
├── 02-UX-UI              screens, interaction, gestures, design system
├── 03-ENGINEERING        architecture, codebase map, AGENT.md (coding rules)
├── 04-SECURITY           threat model, RBAC, secrets audit, authn/authz
├── 05-PERFORMANCE        performance analysis
├── 06-QUALITY            testing, defects, error states, pre-release
├── 07-OPERATIONS         CI/CD, deployment, observability, backup
├── 08-NATIVE-ANDROID     native blueprint + FINAL design-closure set + ADRs
├── 09-IMPLEMENTATION     implementation control system (planning)
└── AUDIT-REPORT*.pdf      consolidated audit reports
```

---

## 🚦 Implementation status

> **Planning / Specification — not yet implemented.** The native rebuild is gated behind explicit product & environment decisions recorded in `docs/09-IMPLEMENTATION/DECISION-REGISTER.md`. The control system is the execution plan, **not** permission to code.

<!-- footer -->
<div align="center">

Built with an obsessive commitment to **truthfulness over appearance** — every screen must really work, every value must be real, every blocked step must be reported, never faked.

</div>
