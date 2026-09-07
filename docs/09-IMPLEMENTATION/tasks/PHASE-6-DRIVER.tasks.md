# Phase 6 — DRIVER Surfaces — Task Contracts

Phase objective: complete DRIVER app surfaces per `10-SANDWORKS/SCREEN-CATALOG.md`, own-scope only. Mandatory sub-checklist as Phase 5. Prereq phases 1-4. Execution gated by Gate-0/env.

### SW-601 Driver shell + nav
Driver Home/Trips/More; own scope; logout. Source: NAVIGATION/SCREEN-CATALOG.

### SW-602 Driver dashboard (+ ADD TRIP primary)
Today's trips, selected tractor, assigned labourers, today's summary, recent trips, **+ ADD TRIP**. Source: SWF-04. Own-scope reads.

### SW-603 Driver add/edit trip (own) + tractor/labourer select
Date/time/tractor(active registry)/labourers(active)/rate snapshot/total/trip number (server). Permitted own-trip edits. Source: SWF-06/07; B-04/05. Rules: number CF-authoritative; snapshot immutable; labourer must exist/eligible. Offline queue + pending. Concurrency: number race → CF. Forms per FORMS-VALIDATION. Tests: trip create/edit (own), multi-driver concurrency, numbering.

### SW-604 Driver my-totals + WhatsApp share
Own work/trip totals; share today's trip count via WhatsApp/share-intent (real values; WhatsApp-optional fallback). Source: SWF-17; B-18. No fabricated values.

### SW-605 Driver profile + notifications (own)
Profile (name/role/phone/picture/status/stats) + own targeted notifications. Photo via Storage if Blaze (SWF-24). Source: SWF-14/16. Read own.
