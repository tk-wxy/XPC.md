# Project Identity (manifest.md)

> **Generated during initialization; rarely changed afterward.**
> This is the shared anchor for every AI agent — whichever agent, platform, or session, they get the
> project's immutable intent from here. Editing this file = amending the project constitution; do it with care.

---

## Mission

- **Name**: {{PROJECT_NAME}}
- **One-line positioning**: {{PROJECT_DESCRIPTION}}
- **Core goals**: {{CORE_GOALS}}
- **Non-goals (explicitly out of scope)**: {{NON_GOALS}}

## Tech stack (locked)

{{TECH_STACK}}

## Architecture invariants

> These are the project's structural bedrock; no agent may overturn them unilaterally. To change one, first
> record the root cause as a new § in decisions.md and get user confirmation before editing this file.

- {{ARCHITECTURE_INVARIANTS}}

## High-risk zones

> Changes here easily cascade into bugs; proceed with extra care.

- {{HIGH_RISK_AREAS}}

## Project structure

{{PROJECT_STRUCTURE}}

## Common commands

{{COMMON_COMMANDS}}

---

> **Rules for editing this file**:
> 1. After init, edit only when the architecture changes fundamentally.
> 2. Record the root cause of any change in decisions.md first.
> 3. Sync all platform entry files after editing.
