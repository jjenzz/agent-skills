# Vertical Slice Delivery

Each slice = one commit + one review. No slice begins until the previous is approved.

## Per-Slice Workflow

Follow this exact sequence. Do not skip steps.

### 1. IMPLEMENT

- Implement exactly one behavioral slice
- Modify only what the slice requires
- Run `git status` to verify changes

### 2. COMMIT

- Stage only files you touched
- Commit with slice ID: `git commit -m "[S1] <message>"`
- Verify: `git log -1 --stat`

### 3. STOP — MANDATORY REVIEW

- DO NOT proceed to the next slice.
- DO NOT write more code.
- DO NOT update todos for the next slice.

Execution MUST transition to the Review role.
Review MUST be performed by a different agent execution than Delivery.
Self-review is prohibited.

If a distinct review agent execution cannot be provisioned,
execution MUST pause and request Human-in-the-Loop review.

Execution MUST NOT continue without review approval.

Implementation hints (non-normative):

| Runtime | Preferred mechanism                          |
| ------- | -------------------------------------------- |
| Cursor  | Spawn a review sub-agent using the Task tool |
| Other   | Pause execution and request HITL             |

### 4. WAIT FOR RESULT

- APPROVED → proceed to next slice
- BLOCKED → fix with `git commit --fixup HEAD`, re-request review

### Common Violations

- Implementing S2 before S1 is reviewed
- Batching multiple slices into one commit
- Skipping review
- Self-approving
- Updating todos before review completes
- Creating placeholder files/folders for future slices

## Behavioral Slices

A slice is a deployable behavior, not a technical task.

Each slice MUST:

- Describe what a user can do, not what code changes
- Span all required layers (UI, logic, data, tests)
- Be independently releasable without future slices

Validation rule: Can a user demo this slice alone? If not, merge or reorder slices.

Planning template:

```
Slice ID:
Behavior:
Includes:
Cleanup:
```

## Delivery and Review Roles

Two roles per slice:

- **Delivery** — writes code, commits
- **Review** — sub-agent that audits committed code, approves or blocks

You MUST spawn a sub-agent for review. Human review is fallback only.
Self-approval is prohibited.

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
git commit -m "[S1] <message>" -m "- <summary bullet 1>\n- <summary bullet 2>"
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
   - Independently releasable
   - No speculative abstraction
   - Generalises only concrete reuse
   - Acceptable performance
   - Tests assert behavior (if applicable)
   - Obsolete code removed (dead branches, unused helpers/configs/tests)

3. Return: APPROVED or BLOCKED with reasoning
```

After sub-agent returns:

- APPROVED → proceed to next slice
- BLOCKED → `git commit --fixup HEAD`, spawn review sub-agent again

## Code Guidelines

Improve code you touch. Each slice should leave the codebase better than before, constrained by these rules:

- Fix issues in code you modify for the slice (not unrelated files)
- Remove obsolete code made unreachable by the slice (components, branches, helpers, configs, tests)
- Remove duplication when a pattern emerges across 3+ instances you touch
- Generalise only when the slice introduces multiple concrete usages
- Never abstract speculatively — all changes must serve the slice's behavior
- Copy-paste is acceptable while waiting for patterns to emerge
- No placeholder files, empty directories, or stubs for future work — if not required for the slice's behavior, don't create it

The goal: incremental improvement without scope creep. Every slice makes the touched code slightly better.

## State Recovery

Prefer git over conversation context:

- `git log <base-branch>..HEAD` — existing slices
- `git show <commit>` — specific changes

## Safety

If review cannot occur, stop.
If a command fails unexpectedly, stop and ask.
