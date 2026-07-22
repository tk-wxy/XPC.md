# Current-State Snapshot & Recent Sessions (memory.md)

> **Trust level: Low (goes stale).** This file is a *directional lead*, not the truth. On cold start, use it to
> quickly locate "roughly what the project is doing and what's next"; but before touching specific code, commands,
> or state, **verify against source / the actual environment first**. On conflict, the code wins — fix this file at wrap-up.

> **Maintenance discipline (best-effort, need not be perfect)**:
> 1. Try to update §0 before each session ends — it's a signpost for the next conversation, not a deliverable.
> 2. §0 is an **overwrite** short snapshot: only "current state + next step", no detail pile-up.
> 3. 🚨 **§0 red line: never accumulate a per-session changelog in §0.** §0 always has only the 4 bullets below,
>    overwritten (not appended). To record "what was done this time" → write §0A (rolling window ≤3), age out to `history.md`.
>    (This red line comes from a real lesson: if §0 is allowed to accumulate a session log, after dozens of sessions
>    it balloons to tens of thousands of chars, slows every cold start, and silently goes stale.)
> 4. Keep §0A a rolling window of ≤ 3 sessions; when writing the 4th, migrate the oldest wholesale to `history.md`.
> 5. Single source of truth: project identity → `manifest.md`, pitfalls/iron-rules → `rules.md`, decisions → `decisions.md`. This file only answers "where are we now".

---

## §0 Current State (overwrite)
> Only the 4 bullets below; overwrite, never accumulate a history log (see the red line).
- **Project status**: [one line on what development stage we're at]
- **Latest progress**: [the core thing recently completed]
- **Todo / next step**: [the top task coming up]
- **Known issues**: [current unresolved / deferred bugs; write "none" if none]

---

## §0A Recent Session Details (rolling window ≤ 3)
*Separate each session with `###`; use multi-line short bullets for key steps and conclusions.*

### Session [YYYY-MM-DD HH:MM] - [short topic]
- [point 1]
- [point 2]

---

## Pointers (this file does not duplicate the following)
- Project identity / stack / structure / commands → `manifest.md` (high trust)
- Hard rules & dead ends → `rules.md` (high trust, historical fact)
- Decision root causes → `decisions.md` (high trust, with evidence)
- Archived sessions → `history.md`
