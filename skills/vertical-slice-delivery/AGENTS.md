# Vertical Slice Delivery

Each slice = one commit + one review. No slice begins until the previous is approved.

## Per-Slice Workflow

Follow this exact sequence. Do not skip steps.

### 1. PRE-FLIGHT (DELIVERY) — REQUIRED

Before writing code, output this checklist and confirm each item:

- `git status` is clean
- Base branch resolved (see `## Git Discipline` below)
- Slice ID chosen (e.g. S1)
- Review agent planned and prompt ready

If any item is not satisfied, STOP and resolve it before continuing.

### 2. IMPLEMENT (DELIEVRY)

- Implement exactly one behavioral slice
- Modify only what the slice requires
- Run `git status` to verify changes
- If the slice is described as `UI-only`, `backend-only`, or `tests later`, STOP and rewrite it as an end-to-end behavior slice first

HARD STOP GUARD: If any file changes exist and you are not in the COMMIT step, STOP and commit the current slice before making further edits.

### 3. COMMIT (DELIVERY)

- Stage only files you touched
- Commit with conventional message + slice ID suffix
  - Format: `feat(scope): [S1] description`
  - Include 1–3 bullet summary lines in the body
- Verify: `git log -1 --stat`

### 4. STOP — MANDATORY REVIEW (REVIEW)

- DO NOT proceed to the next slice.
- DO NOT write more code.
- DO NOT update todos for the next slice.

Execution MUST transition to the Review role. Review MUST be performed by a different agent execution than Delivery. Self-review is prohibited.

If a distinct review agent execution can be provisioned, you MUST spawn a sub-agent for review. Human review is fallback only. Execution MUST NOT continue without review approval. Self-approval is prohibited.

RECOVERY: If changes were made without a slice commit, STOP immediately, create the slice commit, and request review before any additional edits.

### 5. WAIT FOR RESULT (DELIVERY)

- APPROVED → proceed to next slice
- BLOCKED → fix with `git commit --fixup HEAD`, re-request review

## Behavioral Slices

A slice is a deployable behavior, not a technical task.

Each slice MUST:

- Describe what a user can do, not what code changes
- Span all required layers for that behavior (`UI`, `domain/data`, `state transitions`, `executable verification`)
- Be independently releasable without future slices

Validation rule: Can a user demo this slice alone in a production environment? If not, squash or reorder slices.

Bad slice: `Add delete button UI to chat history item.`
Good slice: `User can delete a chat history item.`

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

If this fails or returns nothing, ask the user for the base branch.

### Before Starting

- `git status` — check for uncommitted changes
- `git log <base-branch>..HEAD --oneline` — see existing slices

### Committing

```bash
git restore --staged :/ && \
git add "path/to/file1" "path/to/file2" && \
git commit -m "feat(scope): [S1] description" -m "- <summary bullet 1>\n- <summary bullet 2>"
```

Quote paths with special characters. Include slice ID in message.
Commit messages MUST include a short summary body (1–3 bullets) describing the behavioral change.

### Fixing Committed Slices

Amendments to existing slices must use fixup **and** autosquash immediately:

```bash
git commit --fixup <commit-hash>
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

After sub-agent returns:

- APPROVED → proceed to next slice
- BLOCKED → `git commit --fixup HEAD`, spawn review sub-agent again

## Code Guidelines

Improve code you touch. Each slice should leave the codebase better than before, constrained by these rules:

- Fix issues in code you modify for the slice (not unrelated files).
- Remove obsolete or unreachable code introduced by the change (components, branches, helpers, configs, tests).
- Prefer direct, local, explicit code over indirection.
- Do not introduce single-use extractions, abstractions, or indirection layers, unless they clearly improve readability, enable concrete reuse, or are lint-driven.
- Allow duplication until a concrete reuse pattern exists; copy-paste is acceptable temporarily.
- Only extract/generalize when BOTH are true:
  1. 3+ real call sites exist, **counting both existing code and the new code you’re adding**, and
  2. the abstraction directly supports shipped behavior (not “cleanliness”).
- Avoid speculative abstractions, placeholders, or future scaffolding.
- Keep control flow easy to trace via IDE navigation and search. Developers should be able to quickly identify:
  1. what triggers behavior
  2. what executes next
  3. where handlers are defined
- Prefer unidirectional dependency/data flow where practical.
- Prefer derived values over redundant stored state.
- Contain side effects; avoid hidden mutations or implicit behavior.
- Maintain acceptable algorithmic complexity for expected scale (prefer O(n) over O(n²) when lists can grow).
- Avoid premature performance optimizations unless profiling or constraints justify them.
- Add tests where they reduce meaningful user-facing or diagnostic risk.
- Prefer boring, obvious solutions unless complexity is clearly reduced.

The goal: incremental improvement without scope creep. Every slice makes touched code slightly better.

## State Recovery

Prefer git over conversation context:

- `git log <base-branch>..HEAD` — existing slices
- `git show <commit>` — specific changes

## Safety

If review cannot occur, stop.
If a command fails unexpectedly, stop and ask.
