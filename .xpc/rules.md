# Iron Rules, Dead Ends & Lookup Table (rules.md)

> **Trust level: High (historical fact).** This records real pitfalls hit and technical routes proven to fail —
> **it happened, so it's true**, the least-rotting and most valuable part of this knowledge base. Check here
> first before acting, to avoid re-stepping the same pit.
>
> **Don't delete invalidated entries.** After a stack/dependency upgrade, a dead end may no longer hold. Mark it
> with strikethrough, keeping the history so no one retries it:
> `~~original route → reason~~ (invalidated YYYY-MM, because …)`.
>
> **Note**: this is {{PROJECT_NAME}}'s set of hard constraints. Violating an iron rule typically causes cascading
> bugs or architectural collapse. Follow them strictly during development.

---

## Index (cold start reads only this section)

*One line each: id | one-line summary | section | high-risk. Sync this table whenever you add/change/remove a rule.*

| ID | Summary | Section | High-risk |
|----|---------|---------|-----------|
| R1 | [one-line summary] | Core architecture | [yes/no] |

> **Reading rule**: on cold start / session start, scan only this table + the dead-ends list; read a section in
> full only when the task touches it (or it's flagged high-risk).

---

## Iron Rules
*Grouped by subsystem/module. Format per rule: [do / don't] + [one-line reason] + [DECISIONS § pointer]*

### Core architecture / global
- [ ] R1: [do what / don't do what] - [one-line reason] - see decisions.md §[id]
- [ ] R2:

### {{HIGH_RISK_AREAS}} (high-risk zone)
- [ ] R3:

*(Hint: the above is a skeleton — grow it from real pitfalls. Sync the top index on every add/change.)*

---

## Dead Ends
*Technical routes proven to fail. Avoid repeating them. Format: route → failure reason → date verified.*
*When invalidated, don't delete — use strikethrough: `~~route → reason → date~~ (invalidated YYYY-MM)`*

- [route description] → [failure reason] → [YYYY-MM-DD]
-

---

## Lookup Table (Troubleshooting)
*Given a symptom, which iron rule to check first. Format: symptom → rule id.*

- [symptom description] → check R[id]
-
