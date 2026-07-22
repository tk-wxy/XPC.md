# Current-State Snapshot & Recent Sessions (memory.md)

> **Trust level: Low (goes stale).** This file is a *directional lead*, not the truth. On cold start, use it to
> quickly locate "roughly what the project is doing and what's next"; before touching specific code/commands/state,
> verify against source first. On conflict, the code wins — fix this file at wrap-up.

> **Maintenance discipline**:
> 1. §0 is an **overwrite** short snapshot. 🚨 **Red line: never accumulate a per-session changelog in §0; only the 4 bullets below.**
> 2. Keep §0A a rolling window of ≤ 3 sessions; when writing the 4th, migrate the oldest wholesale to `history.md`.
> 3. Single source of truth: identity → `manifest.md`, pitfalls/iron-rules → `rules.md`, decisions → `decisions.md`. This file only answers "where are we now".

---

## §0 Current State (overwrite)
> Only the 4 bullets below; overwrite, never accumulate a history log.
- **Project status**: MVP works. `add` / `log` / `sync` are stable; `watch` just gained debounced batch commits.
- **Latest progress**: changed `watch` from commit-per-change to an 800ms debounced batch commit, to avoid a flurry of tiny commits from rapid notes (see §0A).
- **Todo / next step**: friendly conflict message for `sync` (currently a rebase conflict just throws the raw git error); add vitest cases for the Windows rename retry.
- **Known issues**: `watch` still occasionally misses events under WSL `/mnt/c` (R7 polling mitigates but doesn't fully fix); `sync` conflict output is unfriendly to users.

---

## §0A Recent Session Details (rolling window ≤ 3)
*Separate each session with `###`; use multi-line short bullets.*

### Session 2026-05-06 10:20 - watch → debounced batch commit
- Symptom: under `watch`, jotting 5 notes in a row → 5 tiny commits in git log, `log` hard to read.
- Fix: `watch.ts` collects change events, triggers one `sync` only after 800ms of no new events (const `WATCH_DEBOUNCE_MS`).
- Reuses sync.ts's serialization lock (R3); if the previous sync hasn't finished when the debounce window ends, it queues.
- Verify: `npm test` passes; manually jotting 5 in a row → 1 commit.

### Session 2026-05-03 16:40 - pull → --rebase, fix history pollution
- Symptom: after back-and-forth sync on two machines, `notch log` was full of merge commits.
- Root cause: `simple-git`'s default `pull` merges. → set R8 (extends existing decision §1's sync strategy, no new §), recorded a dead end in rules.
- Changed `sync.ts` pull to `--rebase`; on conflict, abort and prompt (message still rough, see §0 todo).

---

## Pointers (this file does not duplicate the following)
- Project identity / stack / structure / commands → `manifest.md` (high trust)
- Hard rules & dead ends → `rules.md` (high trust, historical fact)
- Decision root causes → `decisions.md` (high trust, with evidence)
- Archived sessions → `history.md`
