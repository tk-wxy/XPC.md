# Gardening Prompt (garden.md)

> **This is a user-triggered maintenance task, not part of the cold-start / normal-session reading path.**
> The knowledge base gets messy, stale, and bloated as development proceeds — that's normal. Rather than hoping
> every session maintains it perfectly, let the user trigger a focused cleanup every so often (e.g. every 1–2
> weeks, or when `.xpc/` starts to feel messy).
>
> **Usage**: send the whole "Prompt to the Agent" block below to any AI agent.

---

## Prompt to the Agent (copy everything below)

---

Please run an **XPC knowledge-base gardening pass** on this project.

**Important boundary: for this task you are only a "librarian / gardener" — write no business code, change no
features.** Your sole job is to make `.xpc/` **accurate, lean, and trustworthy** again. Read source only to
cross-check; do not modify source.

### Checklist (do each item, then report back to me)

**1. Current-state drift check (memory.md §0)**
- Spot-check each assertion in `§0` (status, latest progress, todos, known issues).
- Cross-check against the real code / git log to see if it still holds.
- List every "doc says ≠ code reality" drift, with a suggested fix.

**2. Pitfall / dead-end review (rules.md)**
- Check whether each iron rule / dead end still holds (has a stack/dependency upgrade invalidated it?).
- Invalidated ones: **do not delete** — mark with strikethrough: `~~original~~ (invalidated YYYY-MM, because …)`.
- Check the top index matches the body; fix if not.
- Merge duplicates if the same kind of pit is recorded more than once.

**3. Decision consistency (decisions.md)**
- Check for decisions already overturned by later development but not marked.
- Keep the superseded old §; note "supersedes §N" in the new §.
- Verify the ToC summaries match the bodies.

**4. Age-out migration (memory.md §0A → history.md)**
- If `§0A` exceeds 3 sessions, migrate the oldest one wholesale to `history.md`, no copy left behind.

**5. Slim down the bloat**
- Find outdated, duplicate, or no-longer-relevant content; flag deletions/archival suggestions.
- Check for single-source-of-truth violations (same fact scattered in multiple places); say where it should converge.

**6. Manifest stability check**
- `manifest.md` is the constitution and shouldn't change often; but if the architecture has fundamentally changed
  and the manifest hasn't kept up, **only point it out — don't edit unilaterally** — and wait for my confirmation.

### Output requirements

- **Give me a "gardening report" first**: grouped by the 6 categories above, list "problem found + suggested action" as short bullets.
- Clearly separate: what you can safely fix directly (index sync, age-out migration, strikethrough marking) vs. what needs my call (deleting content, editing the manifest).
- **Wait for my confirmation before editing files**; you may pre-apply the safe fixes and note them in the report.
- List anything you're unsure about and ask me; don't decide for me.
