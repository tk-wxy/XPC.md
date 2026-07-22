English · **[简体中文](./README.zh-CN.md)**

# XPC — give your AI durable project memory

**Your AI forgets everything each new chat, re-debugs the same pitfalls, and goes blank when you switch models.**
XPC fixes that with a handful of Markdown files — nothing to install, nothing to run. It captures your project's
intent, hard-won pitfalls, and decisions so any agent picks up where the last one left off.

## Use it in one sentence

Clone the framework so its files land **at your project root** (the trailing `.` avoids a wrapper folder):

```bash
mkdir my-project && cd my-project
git clone https://github.com/tk-wxy/XPC.md.git .   # note the "." — clone into here, no wrapper
rm -rf .git && git init                            # make it your own repo
```

> ⚠️ `CLAUDE.md` / `AGENTS.md` / `.xpc/` must sit at your project **root** — the only place an agent auto-reads. If they land in a subfolder, XPC silently does nothing.

Then tell your agent:

> My project is … (describe it). Please initialize.

It reads the entry file (`CLAUDE.md` / `AGENTS.md` / …), runs `.xpc/init.md`, asks for anything missing, bakes the
project constitution, and confirms with you. Every session after that it cold-starts from `.xpc/` — **and works in
your language**, so `请初始化` gets you a Chinese knowledge base just the same.

## See it filled in

**→ [`examples/notch/.xpc/rules.md`](./examples/notch/.xpc/rules.md)** — the `symptom → rule` table *is* the point:
pitfalls a fresh agent would take rounds to rediscover, known from turn one.

## The files (`.xpc/`)

| File | Role | Trust |
|------|------|-------|
| `manifest.md` | Project constitution — mission, stack, invariants, high-risk zones | **High** |
| `rules.md` | Pitfalls, dead ends, `symptom → rule` table | **High** — most valuable |
| `decisions.md` | Why each choice was made, with evidence | **High** |
| `memory.md` | Current-state snapshot (§0) + recent sessions | **Low** — verify before trusting |
| `workflow.md` · `history.md` · `garden.md` | Dev protocol · archive · housekeeping | — |

**Trust levels are the core idea:** pitfalls are permanent facts (trust them); the state snapshot goes stale (a lead
only). That one distinction keeps stale notes from misleading the next agent.

## Know before you adopt

- ✅ Best for solo / small teams iterating with agents on quirky, knowledge-dense projects.
- ⚠️ It doesn't self-maintain — a human prunes it now and then (`.xpc/garden.md`). Value grows over time, not on day one.
- ❌ Not for one-off scripts or work without an AI agent.

## Notes

- **Platforms**: entries for AGENTS.md (recommended), Claude, Gemini, Cursor, Copilot, Windsurf, Cline, Roo, Aider, Augment, Amazon Q — keep what you use, delete the rest.
- **Going minimal?** `AGENTS.md` + `.xpc/manifest.md` + `.xpc/rules.md` alone gets you 80% of the value.

MIT
