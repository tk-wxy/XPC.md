English · **[简体中文](./README.zh-CN.md)**

# XPC — Stop your AI re-stepping the same landmines

> Every new conversation, your AI forgets the project and re-debugs the same pitfalls; switch models and it's total amnesia.
> XPC is a lightweight, archival prompt framework — **just a handful of Markdown files**. It captures project
> intent, hard-won pitfalls, and architecture decisions so **any** AI agent gets productive within minutes of a
> cold start, and never repeats the same mistake.

XPC = **Cross-Platform Coding prompt framework**. In one line: **turn your AI's experience from a throwaway
conversation into an accumulating asset.** Nothing to install — **it's Markdown files + an AI that follows them.**

---

## How to use it (clone → one sentence)

**1. Drop the framework into your new project directory** (clone or copy), and delete the platform entry files you don't use:

```bash
git clone https://github.com/tk-wxy/XPC.md.git my-project
cd my-project
rm -rf .git && git init      # make it your own project's repo
```

**2. Open your AI agent and say:**

> My project is … (describe it). Please initialize.

**That's it.** On the first turn your agent auto-reads the entry file (`CLAUDE.md` / `AGENTS.md` / whichever your
platform uses). Its *initialization bootstrap* routes it into `.xpc/init.md`, and it will:

- Pull what it can from your description, and **ask you for what's missing** (tech stack, high-risk areas) — no guessing
- Detect the project type (greenfield / existing code) and bake `manifest.md` (the project constitution), filling every `{{placeholder}}`
- Initialize `memory.md §0`; keep `rules.md` / `decisions.md` as empty skeletons (rules grow from real pitfalls, never fabricated)
- **Recite the constitution back for your confirmation** before any development

> **Initialization is one-time and works in your language**: the agent runs init and writes the knowledge base in
> whatever language you speak. It then consumes the one-off scaffolding (`.xpc/init.md`, placeholders, this README,
> `examples/`) and keeps the role files + durable constraints. From then on, every session it cold-starts from
> `.xpc/` and follows `.xpc/workflow.md`; say "wrap up" when you're done and it updates the knowledge base.

---

## See what it looks like "filled in"

An empty template is hard to picture. Look at a realistic, already-iterated example first:

👉 **[`examples/notch/`](./examples/notch/)** — the complete XPC knowledge base of a fictional cross-platform CLI tool.

Focus on the `symptom → rule` lookup table in **[`examples/notch/.xpc/rules.md`](./examples/notch/.xpc/rules.md)** —
pitfalls a fresh agent would otherwise take several rounds to rediscover, known from turn one. **That's the core value of XPC.**

---

## Each file has a job (`.xpc/`)

| File | Role | Trust level |
|------|------|-------------|
| `manifest.md` | Project constitution: mission / stack / architecture invariants / high-risk zones (first stop on cold start) | **High** (human-confirmed) |
| `rules.md` | Pitfalls / dead ends / `symptom → rule` lookup table | **High** (it happened, so it's true — **most valuable**) |
| `decisions.md` | Architecture decisions with root cause + evidence | **High** |
| `memory.md` | Current-state snapshot (§0 = 4 bullets) + recent sessions (§0A ≤3) | **Low (goes stale)** — a lead only; verify against source |
| `workflow.md` | The dev protocol (cold-start / session / task-end — the single source of truth) | — |
| `history.md` | Archive (not read by default) · `garden.md` housekeeping · `init.md` one-time init | — |

> **Trust levels are XPC's core stance**: pitfalls/decisions are non-rotting historical facts (most valuable); the
> current-state snapshot goes stale (treat it as a lead only). This defuses the one thing that makes a reference
> actively harmful — stale content mistaken for truth.

---

## Honest boundaries (so you don't expect the wrong thing)

- ✅ **Good fit**: solo / small teams iterating with AI agents over time, platform quirks aplenty, knowledge-dense projects.
- ⚠️ **It doesn't self-maintain**: XPC assumes a **human gardener** prunes it periodically (`.xpc/garden.md`). The
  knowledge base gets messy and stale over time — that's normal; you fix it by tidying, not by hoping the AI does it perfectly.
- ⚠️ **Value is back-loaded**: thin early on; the pitfall library only gets rich after real iterations and real pain.
- ❌ **Not for**: pure manual dev, no AI agent, or one-off throwaway scripts.

## Seamless switching

Switch agents / models without amnesia: the new agent reads its own entry file → runs the cold-start protocol →
reads `manifest.md` first (locks intent, no drift) → then `memory.md` (current state) → picks up immediately, no re-init.

## Supported platform entries

`AGENTS.md` (cross-platform standard, recommended) · `CLAUDE.md` · `GEMINI.md` · `.cursor/rules/*.mdc` ·
`.windsurfrules` · `.github/copilot-instructions.md` · `.clinerules/core.md` · `.roo/rules-code/core.md` ·
`CONVENTIONS.md` (Aider) · `augment-guidelines.md` · `.amazonq/rules/core.md`. Keep the ones you use, delete the rest.

## Lightweight usage

Small / short-lived project? Skip the full set: keep just `AGENTS.md` + `.xpc/manifest.md` + `.xpc/rules.md` —
a constitution plus a pitfall log already gets you 80% of the value.

## License

MIT
