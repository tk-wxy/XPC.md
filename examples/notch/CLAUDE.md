# notch — Claude Code entry

> A cross-platform CLI quick-note tool: one command appends a timestamped thought to a local git repo; `watch` mode auto-syncs.
> Claude Code reads CLAUDE.md recursively from subdirectories; deeper files take priority.

**This file just routes. The single source of truth for all dev flow, protocol, and conventions is `.xpc/workflow.md` — follow it strictly.**

## Startup Protocol

- **First contact (cold start)**: run `.xpc/workflow.md` §0 (Cold-start protocol)
- **Already know the project**: run `.xpc/workflow.md` §1 (Session-start protocol)

## Task End (highest priority)

**Before outputting any summary to the user, first run `.xpc/workflow.md` §4 (Task-end protocol).**
Trigger: task done, session end, or the user says "wrap up". Prioritize recording this session's pitfalls & decisions (non-rotting high-value knowledge); update the current-state snapshot best-effort.

## Core Principles (full version in workflow.md §5)

- Diagnose before modifying; if unsure, ask, don't assume
- High-risk zones (`store.ts` atomic write / `sync.ts` git concurrency / `paths.ts` cross-platform paths): one variable at a time; repeated failure at the same spot = dead-end signal, roll back
- All behavior must align with `.xpc/manifest.md`
