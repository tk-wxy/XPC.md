# Phase 2 Development Protocol (workflow.md)

> **System directive**: this is the core, full-lifecycle development workflow for this project.
> This framework is **reference-first, constraint-second** — treat the knowledge here as high-quality
> *leads* that help you avoid pitfalls and work more reliably, not as ceremony to execute mechanically.

---

## Trust levels (the stance to take before reading any knowledge file)

Files differ in reliability — don't trust them all equally:

| File | Content | Trust level | How to use |
|------|---------|-------------|-----------|
| `manifest.md` | Mission / architecture invariants / high-risk zones | **High** (human-confirmed at init) | Behavioral anchor; align to it |
| `rules.md` | Pitfalls / dead ends / iron rules | **High** (it happened, so it's true) | Check first before acting, to avoid re-stepping |
| `decisions.md` | Decision root causes + evidence | **High** | Consult when you need the "why" |
| `memory.md §0` | Current-state snapshot | **Low (goes stale)** | A directional lead only; **verify against source before trusting** specific code/commands |
| `history.md` | Archived sessions | — | Forensics only; not read by default |

> **On conflict, the real code / actual run result wins.** Fix stale current-state records at wrap-up.

---

## 0. Cold-start protocol (run on first contact with this project)

> Applies to: a new agent's first session, first session after switching models, or re-entry after a long gap.

**Trigger**: if you have no context/memory of this project, run the cold start:

1. **Read identity**: `.xpc/manifest.md` (mission, stack, architecture invariants, high-risk zones)
   - This is the immutable project constitution; all your actions must align with it.
2. **Read current state**: `.xpc/memory.md` in full (§0 current state + §0A recent sessions)
   - Quickly grasp progress, todos, and what happened recently.
3. **Scan iron-rule index**: the index table at the top of `.xpc/rules.md` + the dead-ends list
   - Sections flagged high-risk in the index must be read in full; others on demand.
4. **Scan decisions ToC**: the table of contents at the top of `.xpc/decisions.md`
   - Only scan the ToC to know which decisions exist; no need to read them all.
5. **Read the workflow**: this file (workflow.md) in full
   - Later sessions only re-read the relevant sections on demand.

After cold start you should be able to answer:
- What is this project? What stage is it at?
- What hard constraints must not be touched?
- What should be done next?

---

## 1. Session-start protocol (progressive reading)

> Applies to: an agent that already knows the project starting a new session or task.

1. **Must read**: `§0` in `.xpc/memory.md` (current-state snapshot), to confirm the latest progress.
2. **Read when relevant**: use the `.xpc/rules.md` index to locate sections tied to the current task's module; read only those.
3. **When you need root cause**: scan the ToC of `.xpc/decisions.md`, find the relevant `§`, read only that one.
4. **Forensics**: `.xpc/history.md` is not read by default; grep it only when tracing history.

---

## 2. Task execution flow

1. **Understand the requirement**
   - Fully grasp user intent; if unsure, ask — never assume.
   - Confirm task boundaries and acceptance criteria.

2. **Impact analysis**
   - Which modules are affected by this change?
   - What are the potential risks?
   - Does it touch a high-risk zone (see `manifest.md`)? If so, raise your alert level.

3. **Plan check**
   - Before acting, consult the dead-ends list in `.xpc/rules.md`.
   - Avoid technical routes already proven to fail.
   - Check `.xpc/decisions.md` for relevant past decisions.

4. **Implement**
   - In a high-risk zone, **change one variable at a time**.
   - Diagnose before modifying; find the cause before you act.
   - Extract every tunable parameter into a named constant.
   - **A verified dumb approach beats an unverified clever one.**
   - Stay consistent with the stack and architecture invariants locked in `manifest.md`.

5. **Self-verify**
   - After changes, run verification yourself (test, compile, or execute).
   - If a path can't be run due to environment limits, honestly label it "unverified".

6. **Dead-end detection**
   - If repeated edits to the same spot keep failing, that's a **dead-end signal**.
   - Roll back decisively and re-evaluate; don't grind endlessly.

---

## 3. Special scenarios

### 1. New feature
- Confirm scope and acceptance criteria with the user before starting.
- Assess impact on existing architecture; confirm it doesn't violate the invariants in `manifest.md`.
- Iterate in small, individually verifiable steps.

### 2. Bug fix / debug
- Flow: reproduce → locate root cause → apply minimal fix → verify.
- Always assume the simplest cause first; rule out the basics.
- After fixing, consider whether this pit should become a new iron rule / dead end in `rules.md`.

### 3. Code review
- Check compliance against `rules.md`, item by item.
- Focus on high-risk-zone changes.
- Check whether the change introduced undocumented new coupling or constraints.

### 4. Refactor
- Ensure adequate test coverage before refactoring.
- **Never mix refactor and behavior change**; commit and verify them separately.
- After each refactoring step, verify system behavior is unchanged.
- A refactor must not violate the invariants in `manifest.md`.

---

## 4. Task-end protocol (update the knowledge base at wrap-up)

> **Trigger (any one)**: task done, session about to end, or the user says "wrap up".
>
> Two tiers: **"Must" is non-rotting high-value knowledge — don't skip it; "Best-effort" is volatile state —
> imperfect is fine**, because the next agent verifies against real code anyway. Spend your effort on "Must" first.

### Must — capture non-rotting, high-value knowledge (where this framework earns its keep)

**1. Record pitfalls & dead ends (if you hit one) → `rules.md`**
- These are historical facts, forever useful; they stop the next agent (or future you on another model) from re-stepping.
- Add the new iron rule / dead end / lookup-table entry, and sync the top index.
- Especially platform/environment-level pitfalls — write them down.

**2. Record major decision root causes (if you made one) → `decisions.md`**
- "Why this, what was rejected" — add a `§`, add a one-line summary to the ToC.

### Best-effort — update the volatile current-state lead → `memory.md`

**3. Overwrite `§0` snapshot**
- Reflect the latest state and next step; write signposts, not detail. Incomplete is OK; just don't point the wrong way.

**4. Age-out migration**
- When `§0A` exceeds 3 sessions, migrate the oldest one wholesale into `history.md`, no copy left behind.

### General constraints

**5. Single source of truth**: each fact lives in exactly one place — identity → `manifest.md`, hard rules → `rules.md`, root cause → `decisions.md`, current state → `memory.md`, history → `history.md`. Elsewhere, put a one-line pointer only.

**6. Short lines, no code dumps**: maintain docs as multi-line short bullets; no multi-thousand-char single lines; never paste large code blocks.

> **The knowledge base will get messy and stale as development proceeds — that's normal.** Don't expect every session
> to maintain it perfectly; a periodic, user-triggered "gardening" pass handles the cleanup — see `.xpc/garden.md`.

---

## 5. Collaboration principles (the core spirit)

- Diagnose before modifying.
- One variable at a time in high-risk zones.
- Run your own verification after changes; honestly label paths you couldn't run as "unverified".
- A verified dumb approach beats an unverified clever one.
- Repeated edits to the same spot that keep failing = dead-end signal; roll back decisively.
- Extract tunable parameters into named constants.
- **If unsure, ask — don't assume.**
- All behavior must align with the project mission in `manifest.md`.
