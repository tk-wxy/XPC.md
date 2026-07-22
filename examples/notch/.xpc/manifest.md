# Project Identity (manifest.md)

> **Generated during initialization; rarely changed afterward.**
> This is the shared anchor for every AI agent — whichever agent, platform, or session, they get the
> project's immutable intent from here. Editing this file = amending the project constitution; do it with care.

---

## Mission

- **Name**: notch
- **One-line positioning**: A cross-platform command-line quick-note tool — one command appends a timestamped
  thought to a local git repo; `watch` mode auto-syncs.
- **Core goals**: Make "jot a thought" near-frictionless (`notch "..."` and you're done); notes land as plain
  Markdown, git handles history and multi-machine sync; consistent behavior across Windows / macOS / Linux.
- **Non-goals**: No cloud service / accounts (sync is the user's own git remote); no rich text / editor (only
  append and search); no realtime collaboration.

## Tech stack (locked)

- Runtime: Node.js 20 (ESM) + TypeScript 5
- CLI: `commander`
- File watching: `chokidar` (**not native `fs.watch`**, see decisions §1)
- Git ops: `simple-git`
- Config location: `env-paths` (**do not hand-roll `$HOME`**, see decisions §3)
- Bundling: `tsup`; tests: `vitest`

## Architecture invariants

> These are the project's structural bedrock; no agent may overturn them unilaterally. To change one, first
> record the root cause as a new § in decisions.md and get user confirmation before editing this file.

- **All note writes must be atomic** (temp file + rename), never `writeFile` overwriting in place (see rules, Writes).
- **Git sync is fully serialized**: at most one git op runs at a time (one in-process lock + a cross-process lockfile).
- **Storage-layer paths are always POSIX** (`/` separators, drive-case irrelevant); only the display layer converts to platform separators.
- **Pure CLI, no resident daemon**: `watch` is a foreground process, quits when it exits; state lives only in the git repo.

## High-risk zones

> Changes here easily cascade into bugs; proceed with extra care.

- `src/store.ts` (atomic write + Windows rename retry)
- `src/sync.ts` (git concurrency / lock / conflict handling)
- `src/paths.ts` (cross-platform path normalization — one mistake and everything mismatches after a machine switch)

## Project structure

```
src/
  cli.ts       # commander entry, registers subcommands (add/watch/sync/log)
  store.ts     # note read/write: atomic write (temp+rename+Windows retry), one file per day
  sync.ts      # git sync: add/commit/pull --rebase/push, serialized lock
  watch.ts     # chokidar watch + debounced batch commit
  config.ts    # config location (env-paths) + repo path resolution
  paths.ts     # cross-platform path normalization (store POSIX / display platform-native)
  index.ts     # library entry
test/          # vitest
```

## Common commands

```bash
npm install
npm run dev -- add "a thought"   # run the CLI locally
npm test                          # vitest
npm run build                     # bundle to dist/ via tsup
```

---

> **Rules for editing this file**:
> 1. After init, edit only when the architecture changes fundamentally.
> 2. Record the root cause of any change in decisions.md first.
> 3. Sync all platform entry files after editing.
