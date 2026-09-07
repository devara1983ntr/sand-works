# Phase 8 — Notifications & Alerts Infrastructure — Task Contracts

Phase objective: FCM integration + per-user targeted notifications A-F + OWNER alert (strongest-compliant). Requires FCM creds (SW-BLK-4) + Blaze for CF send. No private financial cross-broadcast; no fake send.
Shared refs: `10-SANDWORKS/NOTIFICATION-ALERT-SPEC.md`, `BACKEND-OPERATIONS.md` (B-13/14), `FEATURE-CATALOG.md` SWF-15/16.

### SW-801 FCM integration + token lifecycle
Title: FCM registration, token refresh/cleanup on role change/disable, POST_NOTIFICATIONS rationale+settings (no broad request), channel model (Earnings/Alerts/System/Work).
Status: NOT-STARTED (implementable: code + local/in-app fallback now). DONE/real-FCM push verify requires **SW-BLK-4** + SW-BLK-1. No fake FCM. Priority P0. Type: backend.
Tests: token lifecycle, permission. Downstream: SW-802/803.

### SW-802 Notification centre + types A-F + deep links + read state
Title: notification centre (per-user), send path CF, deep-link re-auth, read state (offline handled), retention. Types A (daily earnings), B (alert), C (approval), D (assignment), E (expiry), F (operational). Per-user targeting; never broadcast others' money.
Source: NOTIFICATION-ALERT-SPEC. Security: CF send; targeted; no spoofed sender. Tests: targeting, no cross-broadcast, read state, deep-link auth. Downstream: notifications UI in each role phase (wired).

### SW-803 Owner alert/warning delivery + ack
Title: OWNER emergency operational warning (optional message; recipients; high-priority + vibration + custom sound + heads-up where permitted + prominent in-app UI + acknowledgement/dismissal; sender/timestamp/recipient). **Honest Android limits:** never claim full-volume override of silent/DND; no unsafe volume tricks; restrict full-screen intents; use compliant prominent alert.
Source: NOTIFICATION-ALERT-SPEC, SWF-15. Security: owner-only; ack recorded. Tests: alert delivery, ack, honest-limit (no forced volume claim).
