---
name: vertical-slice-delivery
description: Required methodology for planning, ideating, and delivering features or tasks. Use when user asks to "plan", "break down", "implement" or "deliver".
---

# Vertical Slice Delivery

This skill defines default delivery as independently valuable, reviewable vertical slices.

Load and obey its orchestrator `AGENTS.md` that lives next to this `SKILL.md`.

## Coding Guidelines Setup

After the orchestrator permission state, resolve the `code-guidelines` dependency before delivery or review:

1. Check the agent's available skills for `code-guidelines`. If found, read its `SKILL.md` and retain its location as `code_guidelines_path`.
2. If missing, offer to install `skills/code-guidelines` from [jjenzz/agent-skills](https://github.com/jjenzz/agent-skills/tree/main/skills/code-guidelines). Install only with the user's approval, using the current agent's supported installer or skill directory. Do not overwrite an existing installation.
3. After installation, retain its location as `code_guidelines_path` and read its `SKILL.md` before continuing. If installation is declined or unavailable, explain the missing dependency and stop before implementation or review.

Use the shared skill as the source of coding standards; do not maintain a separate copy here. Pass `code_guidelines_path` to delivery and review agents, which must read it themselves. This setup does not depend on a particular agent product, installer, or filesystem location.

## When to Apply

Use this skill whenever work is being:

- planned
- split into tasks or steps
- delivered incrementally
- resumed after interruption

Any deviation from vertical slice delivery MUST be explicit and justified.

## Core Loop

Each slice = plan -> deliver -> quality gate -> review -> commit. No next slice starts until current slice is committed.
