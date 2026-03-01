# Vertical Slice Delivery

## Per-Slice Workflow

Follow this exact sequence. Do not skip steps.

### 1. PRE-FLIGHT (DELIVERY) — REQUIRED

Before writing code, output this checklist and confirm each item:

- `git status` is clean
- Base branch resolved (see `## Git Discipline`)
- `git log <base-branch>..HEAD` plan progress
- Slice ID chosen (e.g. S1)
- Review agent planned and prompt ready

If any item is not satisfied, STOP and resolve it before continuing.

### 2. IMPLEMENT (DELIEVRY)

- Implement exactly one behavioral slice
  - Think: Skateboard → Scooter → Bicycle → Motorcycle → Car
  - Not: Wheels → Chassis → Engine → Body → Car
- Modify only what the slice requires
- Run `git status` to verify changes
- If the slice is described as "UI only", "backend only", or "tests later", STOP and rewrite it as an end-to-end slice

### 3. COMMIT (DELIVERY)

- Stage only files you touched
- Commit with conventional message + slice ID suffix
  - Format: `feat(scope): [S1] description`
  - Always include 1–3 bullet summary in the body describing behavioral change

### 4. STOP — MANDATORY REVIEW (REVIEW)

- DO NOT proceed to the next slice.
- DO NOT write more code.
- Execution MUST transition to the Review role.
- Review MUST be performed by a sub-agent. Human review is fallback only.
- Execution MUST NOT continue without review approval. Self-approval is prohibited.

### 5. WAIT FOR RESULT (DELIVERY)

- APPROVED → squash commmits → proceed to next slice
- BLOCKED → fix with `git commit --fixup HEAD`, re-request review

## Behavioral Slices

A slice is a deployable behavior, not a technical task.

Each slice MUST:

- Describe what a user can do, not what code changes
- Span all required layers for that behavior (`UI`, `domain/data`, `state transitions`, `executable verification`)
- Be independently releasable without future slices

Validation rule: Can a user demo this slice alone in a production environment? If not, squash or reorder slices.

- Bad slice: `Add delete button UI to chat history item.`
- Good slice: `User can delete a chat history item.`

Planning template:

```
Slice ID:
Behavior:
Includes:
Cleanup:
```

## Git Discipline

### Determine Base Branch

Detect the base branch before starting work:

```bash
git symbolic-ref refs/remotes/origin/HEAD | sed 's@^refs/remotes/origin/@@'
```

If this fails or returns nothing, ask for the base branch.

### Committing

```bash
git restore --staged :/ && \
git add "path/to/file1" "path/to/file2" && \
git commit -m "feat(scope): [S1] description" -m "- <summary bullet 1>\n- <summary bullet 2>"
```

Quote paths with special characters. Include slice ID in message.

### Fixing Committed Slices

Amendments to existing slices must use fixup:

```bash
git commit --fixup <commit-hash>
```

### Squashing Commits

```bash
GIT_EDITOR=: GIT_SEQUENCE_EDITOR=: git rebase -i --autosquash <base-branch>
```

## Review Prompt

Pass this prompt to the review sub-agent (substitute SLICE_ID):

```
Review commit for slice [SLICE_ID].

1. Run `git show HEAD`
2. Evaluate:
   - Behavioral correctness
   - Edge cases handled
   - Independently releasable as user-visible behavior (not implementation fragment)
   - Strictly adheres to `## Code Guidelines` in vertical-slice-delivery skill AGENTS.md
   - Tests assert behavior (if applicable)

3. Return: APPROVED or BLOCKED with reasoning
```

## Code Guidelines

Improve code you touch. Each slice should leave the codebase better than before, constrained by these rules:

- Fix issues in code you modify for the slice (not unrelated files).
- Contain side effects; avoid hidden mutations or implicit behavior (CRITICAL).
- Remove obsolete or unreachable code caused by the change (components, branches, helpers, configs, tests).
- Always prefer direct, local, explicit code over indirection.
- Do not introduce single-use extractions or abstractions unless it enables concrete reuse, or is lint-driven.
- Only extract/generalize when BOTH are true:
  1. 3+ real call sites exist, **counting both existing code and the new code you’re adding**, and
  2. the abstraction directly supports shipped behavior (not “cleanliness”).
- Avoid speculative abstractions, placeholders, or future scaffolding.
- Allow duplication until concrete patterns exist; copy-paste is acceptable temporarily.
- Keep control flow easy to trace via IDE navigation and search.
- Prefer unidirectional dependency/data flow.
- Prefer derived values over redundant stored state.
- Ensure acceptable algorithmic complexity (prefer O(n) over O(n²) when lists can grow).
- Add tests where they reduce meaningful user-facing or diagnostic risk.
- Prefer boring, obvious solutions unless complexity is clearly reduced.

The goal: incremental improvement without scope creep. Every slice makes touched code slightly better.
