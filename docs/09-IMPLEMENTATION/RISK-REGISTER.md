# SAND WORKS — Risk Register

Status: **RECONCILED to `10-SANDWORKS`.** Supersedes retired S-V1 register. Planning-only control plane.

## Spec / execution risks (code can mitigate)
| ID | Risk | Likelihood | Impact | Mitigation / owner of mitigation |
|---|---|---|---|---|
| R-1 | Stale 09 plan misleads an agent (the readiness-audit gap) | — (occurred) | High | **This R-1 reconciliation** — 09 superseded banner + SW-xxx inventory + traceability |
| R-2 | Fabrication of Firebase/FCM/Storage/CF/signing infra | Med | Critical | AGENT §14; blockers not disguised; build to boundary only |
| R-3 | Fake success / silent failure in offline flow | Med | High | SW-201..204; explicit pending + conflict; idempotency (SW-203) |
| R-4 | Double earnings on closure re-run | Med | Critical | SW-403 exactly-once per (org,date); CF idempotent; FT-closure |
| R-5 | Money integer/rounding drift | Med | High | SW-108/402 integer paise policy + tests |
| R-6 | Cross-org / role-escalation / forge (approval, rate, number, audit, timestamp) | Med | Critical | SW-306/307/308/309; Security Rules + server-authority + attack tests |
| R-7 | Owner-notification shows another user's money (broadcast) | Low-Med | High | SW-802 per-user targeting; no private-financial cross-broadcast |
| R-8 | Labourer regains write after temp-expiry | Low | High | SW-306 expiry via server time; SW-307(B-11/12) |
| R-9 | Leaderboard fabricated ranks / stale | Med | Med | SW-404 real-data-only; deterministic tie; reset boundaries |
| R-10 | Deep-link authz bypass | Med | High | SW-305 re-validate target; FT-RBAC/nav |
| R-11 | Offline changes lost after process death | Low-Med | Med | SW-203 durable outbox; FT process-death (SW-903) |
| R-12 | Permission over-request (notifications) / full-volume-alert claim | Med | Med | SW-801 rationale flow; SW-803 honest limits |
| R-13 | Accessibility/responsive regression | Med | Med | SW-902 audit gate |

## Environment / go-live risks (owner-input required)
| ID | Risk | Blocks | Smallest input |
|---|---|---|---|
| ENV-1 | No real Firebase project/config | SW-301..309, release (SW-BLK-1) | owner creates project + config |
| ENV-2 | Blaze not decided → Storage/CF/scheduling ambiguous | SWF-24/25/26, cloud paths (SW-BLK-2) | owner decision |
| ENV-3 | No signing keystore | release/private APK (SW-BLK-3) | owner keystore |
| ENV-4 | No FCM creds | notifications/alerts (SW-BLK-4) | provided with project |
| ENV-5 | UX/wireframes unapproved | P5-7 final polish (SW-BLK-6) | owner approval |
| ENV-6 | Alert/notification copy unapproved | final strings (SW-BLK-5) | owner/approved copy |
| ENV-7 | Canonical asset ambiguity (1536 vs 1254/1024) | asset finalisation (SW-BLK-A1/A2) | owner confirmation — do not guess |

## Accepted / residual
Without env inputs, non-cloud foundations (P1/P2 and UI shells/screens that read local data) remain buildable; cloud-backed, money-authority, notification, and release areas are BLOCKED until their blockers clear. This split is intentional and reported (spec vs go-live).
