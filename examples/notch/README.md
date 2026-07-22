# Example: notch's XPC knowledge base (worked example)

> **This is the XPC knowledge base of a fictional project, to show what XPC looks like "filled in".**
> `notch` is an imaginary cross-platform CLI quick-note tool (Node.js + TypeScript). There's **no real source
> code** here — the point is the `.xpc/` knowledge layer: how cross-platform pitfalls, decisions, and iron rules
> get captured so any AI agent, after a cold start, stops re-stepping them.

## Why look at this

The XPC template ships as an **empty skeleton** — rules grow from real pitfalls, nothing pre-fabricated. An empty
skeleton is hard to picture "in use". This example is that "in use" state: a knowledge base from a project that's
been iterated over several sessions and accumulated real experience.

**Read in this order to feel the value:**

1. **`.xpc/rules.md`** — the crown jewel. Look at the `symptom → rule` lookup table:
   "Sporadic EPERM rename on Windows", "notes mismatch after cloning on another machine", "non-ASCII mojibake in the
   Windows terminal" — classic deep pitfalls of a cross-platform CLI. **With this table, the next agent (or future
   you on another model) never re-steps them.**
2. **`.xpc/decisions.md`** — each "why this choice" comes with evidence and rejected alternatives (chokidar vs fs.watch, atomic write vs file lock).
3. **`.xpc/manifest.md`** — the project constitution: mission, stack, architecture invariants, high-risk zones. First stop on cold start.
4. **`.xpc/memory.md`** — current-state snapshot (§0 = 4 bullets) + recent session details (§0A rolling window ≤3).
5. **`AGENTS.md` / `CLAUDE.md`** — the baked platform entries, a thin routing layer.

## Note the trust levels

Every file here carries a **trust level** at the top — XPC's core stance:

- `manifest` / `rules` / `decisions` = **high** (pitfalls are historical fact, barely rot — most valuable)
- `memory.md §0` = **low** (current state goes stale; a directional lead only, verify against source before trusting)

> That's XPC: **reference-first, constraint-second.** It helps the AI avoid pitfalls, work more reliably, and save
> tokens — but it assumes a human gardener prunes it periodically (see the framework's `.xpc/garden.md`); it does not self-maintain.
