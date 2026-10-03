# Antigravity Diagnostic Report

## Summary
Antigravity was diagnosed on Windows after a cache/config purge. The application is **healthy** — no critical issues remain.

## Environment
| Item | Value |
|------|-------|
| OS | Windows |
| App version | 2.19.1 |
| Electron | 44.3.0 |
| Chrome | 152.0.7977.78 |
| V8 | 15.2.124.19 |

## Diagnosis method
- Launched with `--enable-logging --v=1 --log-file=... --remote-debugging-port=9222`
- Captured logs via filesystem (`main.log`, `language_server.log`) and CDP (`http://localhost:9222`)

## Findings

### ✅ Healthy
1. Server initialized successfully in **2.03s**
2. Auth succeeded (`Auth succeeded, refreshing features and managers`)
3. Remote control connected (`Connection status: Connected`)
4. Updater up-to-date (`Up to date (2.19.1)`) — the stale pending update was removed by the purge

### ⚠️ Minor (non-blocking)
1. **Self-signed cert warning** for `127.0.0.1` (`No matching issuer found`) — expected for the app's local HTTPS server
2. **`MaxListenersExceededWarning`** — 11 `storage:changed` listeners added (minor EventEmitter memory-leak warning)
3. **Linux path check** `/usr/bin/cs invalid` — benign cross-platform check on Windows

## Resolution
The cache/config purge (previous step) resolved the primary issue: stale Chromium caches and a corrupted pending updater payload (~312 MB). The app now starts cleanly, authenticates, and reports itself up-to-date.

## Redaction note
Sensitive values (auth tokens, remote-control UUID, staging user ID) have been redacted from this report.
