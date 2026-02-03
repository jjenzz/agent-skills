---
name: vertical-slice-delivery
description: Required methodology for planning, ideating, and delivering features or tasks. Use when user asks to "plan", "break down", "continue", or "figure out steps".
---

# Vertical Slice Delivery

This skill defines the default delivery methodology for planning and shipping
work as independently valuable, reviewable vertical slices.

Load and obey its `AGENTS.md` that lives next to this `SKILL.md`.

## When to Apply

Use this skill whenever work is being:

- planned
- split into tasks or steps
- delivered incrementally
- resumed after interruption

Any deviation from vertical slice delivery MUST be explicit and justified.

## Core Loop

Each slice = one commit + one review. No slice begins until the previous is approved.

1. **IMPLEMENT** — one behavioral slice only
2. **COMMIT** — with slice ID `[S1]`
3. **STOP** — mandatory review (do not proceed)
4. **WAIT** — approved → next slice, blocked → fixup and re-review

## Key Concepts

| Concept           | Summary                                                                     |
| ----------------- | --------------------------------------------------------------------------- |
| Behavioral Slices | Deployable behaviors, not technical tasks. "Can a user demo this alone?"    |
| Role Separation   | Delivery writes code; Review audits and approves. Self-approval prohibited. |
| Git Discipline    | Status before work, explicit paths, fixups for amendments                   |
| Code Guidelines   | Improve touched code only, no speculative abstraction                       |

## Full Document

All rules with examples and details: `AGENTS.md`
