# Iron Rules, Dead Ends & Lookup Table (rules.md)

> **Trust level: High (historical fact).** This records real pitfalls hit and technical routes proven to fail —
> **it happened, so it's true**, the least-rotting and most valuable part of this knowledge base. Check here
> first before acting, to avoid re-stepping the same pit.
>
> **Don't delete invalidated entries.** After a stack/dependency upgrade, a dead end may no longer hold. Mark it
> with strikethrough, keeping the history: `~~original route → reason~~ (invalidated YYYY-MM, because …)`.
>
> **Note**: this is notch's set of hard constraints. Violating an iron rule typically causes cascading bugs or data loss.

---

## Index (cold start reads only this section)

*One line each: id | one-line summary | section | high-risk. Sync this table whenever you add/change/remove a rule.*

| ID | Summary | Section | High-risk |
|----|---------|---------|-----------|
| R1 | Note writes must be atomic (temp+rename), never overwrite in place | Writes | yes |
| R2 | On Windows, rename on a locked target throws EPERM — needs backoff retry | Writes | yes |
| R3 | Git ops fully serialized, one lock + lockfile | Sync | yes |
| R4 | Storage-layer paths always POSIX; only display layer is platform-native | Paths | yes |
| R5 | Use env-paths for config dir, never hardcode $HOME/~ | Config | no |
| R6 | On Windows, force UTF-8 stdout or non-ASCII notes mojibake | Terminal | no |
| R7 | chokidar needs usePolling fallback on network drives / WSL | Watch | no |
| R8 | pull with --rebase; no merge commits polluting history | Sync | no |

> **Reading rule**: on cold start / session start, scan only this table + the dead-ends list; read a section in
> full only when the task touches it (or it's flagged high-risk).

---

## Iron Rules
*Format per rule: [do / don't] + [one-line reason] + [DECISIONS § pointer]*

### Writes (store.ts, high-risk)
- [x] R1: Note writes must go through atomic write — `fs.writeFile(tmp)` → `fs.rename(tmp, target)`; never
  `writeFile(target)` directly. Reason: `watch` or another process may read a half-written file; a crash loses data. See decisions.md §2
- [x] R2: On Windows, `rename` onto a target held open (antivirus / search indexer / editor) throws `EPERM`/`EBUSY`
  — must use exponential backoff retry (default 5 tries, 20ms start). Don't "fix" it as a logic bug; it's platform behavior. See decisions.md §2

### Sync (sync.ts, high-risk)
- [x] R3: All git ops serialized — in-process `AsyncLock` + a repo-root `.notch.lock` cross-process lock; concurrent
  `add/commit` collides on `.git/index.lock`. See decisions.md §1 (converse)
- [x] R8: `pull` is always `--rebase`; merge commits tangle the quick-note history into a mesh and `log` becomes
  unreadable. On conflict, abort and prompt the user to resolve manually; do not auto `-X theirs`.

### Paths (paths.ts, high-risk)
- [x] R4: Paths entering the storage layer / written into git are always `toPosix()`'d first (`\`→`/`, drop
  drive-case sensitivity); only convert with `toPlatform()` when displaying to the user. Reason: storing
  `notes\2026\05.md` on Windows makes the path mismatch and the note "vanish" after a macOS clone.

### Config (config.ts)
- [x] R5: Use `env-paths('notch')` for config and the default repo dir, **not** `path.join(os.homedir(), '.notch')`.
  Reason: Windows has no `$HOME` semantics; macOS should land in `~/Library/Application Support`.

### Terminal (cli.ts)
- [x] R6: On Windows, ensure `process.stdout` is UTF-8 at startup. Reason: the default GBK/legacy code page mojibakes non-ASCII notes on echo.

### Watch (watch.ts)
- [x] R7: `chokidar` must enable `usePolling:true` when it detects a network drive / WSL mount (`\\` UNC or `/mnt/`).
  Reason: inotify/FSEvents are unreliable on those mounts and drop events.

---

## Dead Ends
*Technical routes proven to fail. Format: route → failure reason → date verified. When invalidated, keep with strikethrough.*

- Native `fs.watch` for cross-platform watching → macOS omits filenames, Windows `recursive` behaves inconsistently, Linux needs manual recursion → switched to `chokidar` → 2026-04-18
- `fs.writeFile(target)` overwriting notes in place → `watch` occasionally reads empty/partial files, power loss loses the day's notes → switched to atomic temp+rename → 2026-04-22
- `path.join(os.homedir(), '.notch')` for config → on Windows CI `homedir()` is empty under the service account; macOS should use Library → switched to `env-paths` → 2026-04-25
- git `pull` (default merge) for auto-sync → after back-and-forth multi-machine sync, `log` is all merge commits, timeline scrambled → switched to `pull --rebase` → 2026-05-03

---

## Lookup Table (Troubleshooting)
*Given a symptom, which iron rule to check first.*

- Notes occasionally go empty / truncated, especially on crash or during high-frequency watch writes → not going through atomic write, check R1
- On Windows, sporadic `EPERM: operation not permitted, rename` → target is locked, check R2 retry logic (not a logic bug)
- After cloning on another machine / OS, some notes are "not found" → platform-separator paths stored in git, check R4
- Non-ASCII notes mojibake in Windows cmd/PowerShell → stdout not set to UTF-8, check R6
- `watch` doesn't fire on a shared drive / WSL → polling fallback not enabled, check R7
- `sync` reports `Unable to create '.git/index.lock': File exists` → concurrent git ops, check R3 serialization lock
- `log` timeline scrambled, full of merge commits → pull produced a merge, check R8
