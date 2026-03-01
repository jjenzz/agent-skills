# Vertical Slice Delivery Orchestrator

Parent is an orchestrator only. Parent does not plan, deliver, or review directly.

## Flow

Run in strict order: `PLAN -> DELIVER -> QUALITY_GATE -> REVIEW`.

- `PLAN`: run planning sub-agent
- `DELIVER`: run delivery sub-agent for one slice
- `QUALITY_GATE`: run lint skill against changed files and apply fixes (fallback to local project checks)
- `REVIEW`: run review sub-agent

If review is `BLOCKED`, return to `DELIVER` for fixup, then run `QUALITY_GATE` and `REVIEW` again.
Proceed to next slice only after `APPROVED`.

## Sub-Agent Invocation Contract

When invoking any role sub-agent, parent prompt MUST include this exact line:

`Load and obey role instructions at: [ROLE_INSTRUCTIONS_URI]`

Role instruction URIs:

- PLAN: `<SKILL-ROOT-DIR>/plan/AGENTS.md`
- DELIVER: `<SKILL-ROOT-DIR>/deliver/AGENTS.md`
- REVIEW: `<SKILL-ROOT-DIR>/review/AGENTS.md`

Parent MUST NOT load role instruction files into parent context.
Parent MUST NOT self-approve slices.
