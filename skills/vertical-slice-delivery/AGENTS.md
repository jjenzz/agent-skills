# Vertical Slice Delivery Orchestrator

Parent is an orchestrator only. Parent does not plan, deliver, or review directly.

## Flow

Strictly follow the flow state machine.

Initial state: `PERMISSION`

- `PERMISSION`: ask user "Would you like me to commit each approved slice automatically?"
  - `yes`: set `commit_mode=auto` and goto `PLAN`
  - `no`: set `commit_mode=hitl` and goto `PLAN`
- `PLAN`: run PLAN sub-agent
  - `COMPLETE`: goto `DELIVER` with `plan_file_path`
- `DELIVER`: run DELIVER sub-agent for one slice
  - `COMPLETE`: goto `QUALITY_GATE`
- `QUALITY_GATE`: run lint on changed files
  - `BLOCKED`: goto `DELIVER` with issues and instructions
  - `APPROVED`: goto `REVIEW`
- `REVIEW`: run REVIEW sub-agent
  - `BLOCKED`: goto `DELIVER` with issues and instructions
  - `APPROVED`: goto `COMMIT`
- `COMMIT`: 
  - `commit_mode=auto`: commit approved slice (follow Commit Instructions)
  - `commit_mode=hitl`: STOP, DO NOT proceed to next slice, return to HITL

### Commit Instructions

- Stage only files touched for the approved slice.
- Commit with conventional message and slice ID:
  - `feat(scope): [S1] description`
- Include 1-3 behavior-focused bullets in commit body.

```bash
git restore --staged :/ && \
git add "path/to/file1" "path/to/file2" && \
git commit -m "feat(scope): [S1] description" -m "- <summary bullet 1>\n- <summary bullet 2>"
```

## Sub-Agent Invocation Contract

When invoking any role sub-agent, parent prompt MUST include:

`Load and obey role instructions at: [ROLE_INSTRUCTIONS_URI]`

Role instruction URIs:

- PLAN: `<SKILL-ROOT-DIR>/agents/plan/AGENTS.md`
- DELIVER: `<SKILL-ROOT-DIR>/agents/deliver/AGENTS.md`
- REVIEW: `<SKILL-ROOT-DIR>/agents/review/AGENTS.md`

Parent MUST NOT load role instruction files into parent context.