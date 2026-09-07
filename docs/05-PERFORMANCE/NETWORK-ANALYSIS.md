# Network Analysis (current)

Status: VERIFIED. Commit `2dd2fe4`.

## No network usage
The current product performs **no network I/O**:
- No `INTERNET` permission in `AndroidManifest.xml`.
- No HTTP/WebSocket/grpc packages (no `http`, `dio`, `web_socket_channel`).
- No remote calls, no backend, no CDN; no Firebase.

The only "external I/O" is local file access via `file_picker`/SAF for backup/restore (local to the device/user-chosen storage).

## Implication
- There are no network-latency, retry, timeout, or bandwidth concerns today. "Offline" is not a degraded mode — it is the only mode.
- When the native product introduces Firebase, a real network/latency/offline analysis becomes necessary (see `08-NATIVE-ANDROID/` and `THREAT-MODEL.md`). Offline-first with online reconciliation is PROPOSED.

## Verification status
VERIFIED (no networking stack present).
