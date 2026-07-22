# Architecture Decisions & Pitfall Root Causes (decisions.md)

> **Trust level: High (evidence-backed historical decisions).** Records "why we chose this"; append-only.
> If a decision is later overturned, don't delete the old §; add a new § and note "supersedes §N".

## Table of Contents (decision summaries)
*One line per §; scan the ToC to decide whether to read that section.*

- §1 File watching: chokidar over native fs.watch (cross-platform consistency + polling fallback)
- §2 Note writes use "temp file + rename" atomic write, with Windows EPERM retry
- §3 Config/repo dir via env-paths, not hand-rolled $HOME

---

## Decision Details

### §1 File watching: chokidar vs native fs.watch
- **Final approach**: use `chokidar`, with `usePolling` on network / WSL mounts (R7).
- **Root cause & evidence**: native `fs.watch` is inconsistent across platforms — macOS `change` events often omit
  the filename; Windows `recursive:true` sporadically misses deep dirs; Linux doesn't support recursive and needs
  manual per-dir watches. In `test/watch.spec.ts`, on the same change set fs.watch dropped filenames 3/10 times on
  macOS, chokidar was 10/10 stable.
- **Rejected alternatives & why**: ① hand-wrapping `fs.watch` + recursion — puts all cross-platform divergence on
  us, exactly the dead-end recorded; ② `@parcel/watcher` (faster native) — needs a prebuilt binary, adds install
  size and cross-platform build complexity, not worth it for a quick-note tool's scale.

### §2 Note writes: atomic write + Windows rename retry
- **Final approach**: `writeFile(target.tmp-<rand>)` → `fsync` → `rename(tmp, target)`; on Windows, exponential
  backoff retry (5 tries, 20/40/80…ms) when rename throws `EPERM`/`EBUSY`.
- **Root cause & evidence**: `writeFile(target)` has a "truncated, not yet fully written" window; `watch` or another
  `notch` process reads an empty/partial file, and power loss loses the day's notes. After atomic write,
  `test/store.concurrent.spec.ts` runs 100 concurrent read/writes with zero corruption. The Windows EPERM comes
  from antivirus/search-indexer briefly holding the target handle — rename itself is correct, it just needs a retry;
  measured, retry drops the failure rate from ~0.5% to 0.
- **Rejected alternatives & why**: ① a file lock (`proper-lockfile`) — atomic rename is enough, extra locking is
  over-engineering; ② `unlink` target then rename — creates a larger "target doesn't exist" window, worse.

### §3 Config location: env-paths vs hand-rolled $HOME
- **Final approach**: `env-paths('notch', { suffix: '' })` decides config and default repo location.
- **Root cause & evidence**: `os.homedir()` may be empty or point somewhere unexpected on Windows service accounts
  / CI runners; the macOS convention is `~/Library/Application Support`, not `~/.notch`. env-paths handles all three
  conventions in one call. On CI (GitHub Actions windows-latest) the hand-rolled `$HOME` version failed wholesale;
  switching to env-paths turned it green.
- **Rejected alternatives & why**: hand-implementing `XDG_CONFIG_HOME` — only covers the Linux convention, still
  needs Windows/macOS special-casing, i.e. reinventing env-paths.
