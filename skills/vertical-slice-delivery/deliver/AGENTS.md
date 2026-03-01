# Vertical Slice Delivery — Deliver Agent

This file defines the DELIVERY role only.

The delivery agent implements exactly one slice, commits it, and then returns control to parent.

## Per-Slice Workflow

Follow this sequence. Do not skip steps.

### 1) PRE-FLIGHT — REQUIRED

Before writing code, output this checklist and confirm each item:

- `git status` is clean (or unrelated changes are explicitly acknowledged)
- Base branch resolved (see `## Git Discipline`)
- `git log <base-branch>..HEAD` reviewed for prior slice context
- Slice ID chosen (for example, `S1`)
- Review handoff notes prepared

If any required item is not satisfied, STOP and return a blocked pre-flight result to parent.

### 2) IMPLEMENT

- Implement exactly one behavioral slice.
- Modify only what this slice requires.
- If slice framing is "UI only", "backend only", or "tests later", STOP and return for re-planning.
- Run `git status` to verify intended file changes.

### 3) COMMIT

- Run formatter against changed files.
- Stage only files touched for this slice.
- Commit with conventional message and slice ID:
  - `feat(scope): [S1] description`
- Include 1-3 behavior-focused bullets in commit body.

### 4) RETURN TO PARENT (MANDATORY)

After commit, stop coding and return:

- Pre-flight checklist status
- Changed file list
- Commit hash and message
- Reviewer notes (what changed and why)

Do not proceed to another slice. Do not self-review.

## Git Discipline

### Determine Base Branch

```bash
git symbolic-ref refs/remotes/origin/HEAD | sed 's@^refs/remotes/origin/@@'
```

If this fails, ask parent to provide base branch.

### Committing Pattern

```bash
git restore --staged :/ && \
git add "path/to/file1" "path/to/file2" && \
git commit -m "feat(scope): [S1] description" -m "- <summary bullet 1>\n- <summary bullet 2>"
```

### Fixups After Review

If parent sends blocked findings, fix with:

```bash
git commit --fixup HEAD
```

Then return to parent for re-review.

## Code Guidelines

Improve touched code without scope creep:

- Fix issues in files you touch for this slice.
- Contain side effects; avoid hidden mutations or implicit behavior.
- Remove obsolete/unreachable code caused by the change.
- Always prefer direct, local, explicit code over indirection.
- Avoid speculative abstractions and placeholders.
- Do not introduce single-use extractions or abstractions unless it enables concrete reuse, or is lint-driven.
- Extract/generalize only when there are 3+ real call sites (counting both existing and new code).
- Allow duplication until concrete patterns exist; copy-paste is acceptable.
- Prefer unidirectional dependency/data flow.
- Prefer derived values over redundant stored state.
- Ensure acceptable algorithmic complexity (prefer O(n) over O(n²) when lists can grow).
- Add tests where they reduce meaningful user-facing or diagnostic risk.
