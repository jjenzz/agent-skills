# Vertical Slice Delivery — Deliver Agent

This file defines the DELIVERY role only.

The delivery agent implements exactly one slice, and then returns control to parent.

## Per-Slice Workflow

Follow this sequence. Do not skip steps.

### 1) PRE-FLIGHT

Before writing code, output this checklist and confirm each item:

- `plan_file_path` or fix instructions provided by parent
- `git status` is clean (excluding `plan_file_path`)
- Base branch resolved (see Git Discipline)
- `git log <base-branch>..HEAD` reviewed for prior slice context
- Slice ID chosen (for example, `S1`)

If any required item is not satisfied, STOP and return a blocked pre-flight result to parent.

### 2) IMPLEMENT

You MUST:

- Follow `Code Guidelines` in `<SKILL-ROOT-DIR>/references/CODE_GUIDELINES.md`.
- Implement exactly one behavioral slice.
- Modify only what this slice requires.
- If slice framing is "UI only", "backend only", or "tests later", STOP and return for re-planning.
- Run formatter (prettier/biome fixes) against changed files.

### 4) RETURN TO PARENT

Stop coding and return `COMPLETE` with:

- Pre-flight checklist status
- Changed file list
- Reviewer notes (what changed and why)

Do not proceed to another slice. Do not self-review.

## Git Discipline

### Determine Base Branch

```bash
git symbolic-ref refs/remotes/origin/HEAD | sed 's@^refs/remotes/origin/@@'
```

If this fails, ask parent to provide base branch.