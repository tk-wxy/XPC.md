# History Archive (history.md)

> **Note**: this file archives aged-out entries from `memory.md` §0A (beyond the rolling window).
> **Not read by default** — grep it only for forensics. Ordered newest-first.

## 2026-04

### Session 2026-04-25 14:10 - config location → env-paths
- Symptom: on Windows CI (windows-latest), every config read/write test failed wholesale.
- Root cause: `os.homedir()` was empty under the runner's service account → config path became a relative dir.
- Fix: switched to `env-paths('notch')`, set rules R5 + decisions §3. CI turned green.

### Session 2026-04-22 09:30 - note writes → atomic write
- Symptom: under `watch` stress, occasional empty-file reads; a forced power-off zeroed the day's notes.
- Root cause: `fs.writeFile(target)` has a half-written window.
- Fix: temp + rename atomic write, set rules R1 + decisions §2; recorded a dead end for "overwrite in place".

### Session 2026-04-18 11:00 - watching from fs.watch to chokidar
- Symptom: on macOS, watch couldn't get the changed filename; on Windows, deep dirs dropped events.
- Fix: switched to chokidar, set decisions §1 + a dead end for "native fs.watch".
