---
name: vertical-slice-delivery
description: Required methodology for planning, ideating, and delivering features or tasks. Use when user asks to "plan", "break down", "implement" or "deliver".
---

# Vertical Slice Delivery

This skill defines default delivery as independently valuable, reviewable vertical slices.

Load and obey its orchestrator `AGENTS.md` that lives next to this `SKILL.md`.

## When to Apply

Use this skill whenever work is being:

- planned
- split into tasks or steps
- delivered incrementally
- resumed after interruption

Any deviation from vertical slice delivery MUST be explicit and justified.

## Core Loop

Each slice = plan -> deliver -> quality gate -> review.
No next slice starts until current slice is `APPROVED`.

## Full Document

Orchestration rules: `<SKILL-ROOT-DIR>/AGENTS.md`
Role rules are resolved by the orchestrator at sub-agent invocation time.
