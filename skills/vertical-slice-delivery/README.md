# Vertical Slice Delivery

An Agent Skill for disciplined, incremental software delivery using **Vertical Slice Architecture**, clean Git history, and stateless execution.

Install with [skills.sh](https://skills.sh).

```sh
npx skills add jjenzz/agent-skills --skill vertical-slice-delivery
```

## Vertical Slice Architecture

**Vertical Slice Architecture** organizes work by **features** rather than technical layers. Each slice contains everything needed to deliver a behavior — UI, logic, data, tests — as a single, releasable unit.

In this skill, **each vertical slice maps to exactly one Git commit**.

## Incremental, Releasable Progress

Every slice must be usable and releasable on its own:

<img width="609" height="411" alt="Illustration showing incremental delivery: Skateboard to Scooter to Bicycle to Motorcycle to Car" src="https://github.com/user-attachments/assets/d1b5f59e-b964-438a-b3ef-f21fde5fe72e" />

**Skateboard → Scooter → Bicycle → Motorcycle → Car**

Not: Wheels → Chassis → Engine → Body → Car

No slice may exist solely to support a future slice. Each commit represents the **best possible version of the product at that stage**.

## What This Skill Enforces

- **Behavior-first slicing** — end-to-end, not layer-by-layer
- **Releasable increments** — every commit is deployable
- **Clean Git history** — fixup + autosquash to keep commits focused
- **Constrained Boy Scout Rule** — refactors only when required by new behavior; discovered issues squash into slice that owns them
- **Git as external memory** — state recovery via `git log`, not conversation history, keeping context small and costs low

## License

MIT
