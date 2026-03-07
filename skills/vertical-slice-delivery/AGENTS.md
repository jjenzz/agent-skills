# Vertical Slice Delivery Orchestrator

Parent is an orchestrator only. Parent does not plan, deliver, or review directly.

## Flow

Run in strict order: `PLAN -> DELIVER -> QUALITY_GATE -> REVIEW`.

- `PLAN`: run planning sub-agent
  - `COMPLETE`: return `plan_file_path`
- `DELIVER`: run delivery sub-agent for one slice with `plan_file_path` or fixup instructions
- `QUALITY_GATE`: run lint against changed files and apply fixes
- `REVIEW`: run review sub-agent
  - `BLOCKED`: run `FIXUP`
  - `APPROVED`: run `COMMIT` then proceed to next slice

### COMMIT (REQUIRED)

When `APPROVED`:

- Stage only files touched for the approved slice.
- Commit with conventional message and slice ID:
  - `feat(scope): [S1] description`
- Include 1-3 behavior-focused bullets in commit body.

```bash
git restore --staged :/ && \
git add "path/to/file1" "path/to/file2" && \
git commit -m "feat(scope): [S1] description" -m "- <summary bullet 1>\n- <summary bullet 2>"
```

If commit creation fails, do not advance to the next slice.

### FIXUP

When `BLOCKED`, goto `DELIVER` and share findings with fixup instructions:

```bash
git commit --fixup HEAD
```

Then proceed with `QUALITY_GATE -> REVIEW`.

## Sub-Agent Invocation Contract

When invoking any role sub-agent, parent prompt MUST include:

`Load and obey role instructions at: [ROLE_INSTRUCTIONS_URI]`

Role instruction URIs:

- PLAN: `<SKILL-ROOT-DIR>/plan/AGENTS.md`
- DELIVER: `<SKILL-ROOT-DIR>/deliver/AGENTS.md`
- REVIEW: `<SKILL-ROOT-DIR>/review/AGENTS.md`

Parent MUST NOT load role instruction files into parent context.