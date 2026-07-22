# Initialization Protocol (init.md) — one-time

> **This is a one-time initialization protocol.** Triggered by the entry file when the project is
> not yet initialized AND the user asks to initialize.
> Purpose: bake this **generic XPC skeleton** into a **project-specific** knowledge base.
> Initialization = digest the generic skeleton into "a structure comfortable for the AI" — consume the
> one-off scaffolding, keep a few role-specific md files + durable constraints.
> **This file may be deleted afterward** (see §4).

> 🌐 **Work in the user's language.** Conduct the initialization dialogue **in the language the user speaks**,
> and bake the knowledge base (manifest / memory / rules / …) in **that same language**. The template ships in
> English as the canonical source, but the project you produce should match the user's language.

---

## Trigger

In a freshly cloned project, the user says something like "**My project is …, please initialize**". You (the AI),
having read the entry file's *Initialization Bootstrap* pointer, run this protocol.

## 1. Understand the project (ask when unsure; don't assume)

Extract what you can from the user's description: **project name, one-line positioning, core goals.**
Then **actively fill the gaps** — the following must be asked, not assumed:

- **Tech stack / platform** (language, framework, runtime) — the foundation; wrong choice, everything skews.
- **Highest-risk zone** (the part where a change most easily cascades into bugs) — if none, write "none yet, add on first hit".
- **Non-goals** (things explicitly out of scope) — optional.

Also determine the project type:

- **Greenfield**: the directory has essentially no code → **plan** the manifest from "requirement + the stack you
  confirmed with the user"; write project structure/commands as "planned, to land as development proceeds".
- **Existing project**: the directory has code → **scan the code first** (structure, core modules, build/test
  commands, obvious high-risk zones), then bake the manifest; evidence-based pitfalls visible in the code
  (`HACK`/`FIXME`/platform constraints/unusual config) may be the **first seed** in rules (cite the evidence).

## 2. Bake the knowledge base

1. **`.xpc/manifest.md` (project constitution, most important)**: fill in mission, stack, architecture invariants, high-risk zones, project structure, common commands.
2. **Entry files** (CLAUDE.md / AGENTS.md / your platform's entry): replace all `{{placeholders}}` with real project info; you may tune the wording to your own strengths.
3. **`.xpc/memory.md`**: write the current-state snapshot in §0 (**4 bullets only**, obey the red line); record this init session in §0A.
4. **`.xpc/rules.md` / `.xpc/decisions.md`**: keep as empty skeletons; seed the first entry **only** if you find real evidence — **never fabricate**. Better an empty skeleton than fake experience.

## 3. Recite for confirmation (required)

Recite the core conclusions of `manifest.md` back to the user (**mission / architecture invariants / high-risk
zones / stack**) and get explicit confirmation. If there's a discrepancy, fix and re-recite. **Do not start
development before confirmation.**

## 4. Wrap-up (embodying "one-time")

After confirmation:

- ✅ **You may delete this file `.xpc/init.md`** — initialization is one-time; discard when done.
- ✅ **You may delete the pure-scaffolding fill-in notes** in each file (e.g. "(hint: this is a skeleton…)", "[fill in]", "[one-line summary]" placeholders) — they've served their purpose.
- ✅ **You may delete the framework's bundled `README.md` / `README.zh-CN.md` / `examples/`** (if present) — those are the framework repo's docs and demos, not your project's content.
- 🚫 **Never delete or alter**: the dev/wrap-up protocol in `workflow.md`, the **trust levels** and **maintenance discipline** headers at the top of each file, the **§0 red line** in `memory.md` — these are durable constraints used **every session**, not one-time scaffolding.

> In short: **consume what's meant to be consumed (the init protocol, fill-in placeholders, skeleton hints, README/examples); leave what's meant to persist (roles, protocol, constraints) untouched.**
> Final form = a few role-specific md files (manifest = identity / rules = pitfalls / decisions = decisions / memory = current state / history = archive) + the workflow protocol + trust & maintenance constraints.
