# Analytics Event Specification

Status: Phase 0.5 (PROPOSED). Meaningful events only; no unnecessary PII; aggregates keyed by role/feature, not personal identity where possible.

## Principles
- Event = action that informs product decisions; skip high-noise/no-value pings.
- No PII in event params (no names, phone, exact locations). Use ids/categories only.
- Session/user identity via Auth id (analytics) not embedded params.
- Consent/privacy respected (D-8); events tagged by category.

## Event catalogue
| Event | Trigger | Params (no PII) | Notes |
|---|---|---|---|
| session_start / app_open | launch | role, screen | default |
| login_success / login_failure | login | method, reason(cat) | no enumeration detail |
| screen_view | nav | screen_id, role | funnel |
| work_session_created | BO-1 | date, session | |
| trip_created | BO-2 | session, driverId, has_labour | |
| trip_edited | BO-2 | id | |
| attendance_recorded | BO-3 | count_present,count_absent | aggregate counts only |
| attendance_corrected | BO-3 | has_reason | |
| trip_deleted | BO-4 | reason_given | |
| session_closed | BO-5 | trip_count | |
| work_assigned | BO-6 (D-6) | driverId | |
| work_status_change | BO-7 (D-6) | from,to | |
| crew_added/edited | BO-8 | kind | |
| user_invited/role_changed | BO-9/10 | new_role | owner metric |
| announcement_sent | BO-11 | audience | |
| backup_started/complete/failed | BO-12/13 | size,state | |
| export_downloaded | BO-14 | period | |
| report_viewed | N-27 | period,type | |
| search_performed | search | has_results | no query text |
| filter_applied | filter | type | |
| notification_opened | tap | event_type | deep-link success |
| notification_permission | prompt | granted/denied/rationale | |
| offline_used / sync_completed | connectivity | queued_count | field usage offline |
| sync_conflict | reconcile | count | reliability |
| error_occurred | typed error | error_code(cat) | no raw message/PII |
| a11y_* (optional) | — | — | only if needed |
| export/search/filter/sort interactions | UI | screen | UX |

## Anti-patterns (avoid)
- No continuous scroll/tap pings per keystroke (except debounced search intent).
- No lat/long streaming unless a feature requires it (none).
- No personal identifiers embedded; id hashing if ever needed.
- Respect Analytics opt-out.

## Verification
Event list PROPOSED; wires to ANALYTICS events in product dashboards; docs in OBSERVABILITY-ARCHITECTURE; test that events fire on the right UI actions (unit), no PII (lint/review), consent respected.
