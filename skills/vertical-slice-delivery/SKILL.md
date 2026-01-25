---
name: vertical-slice-delivery
description: Enforces vertical slice planning, incremental delivery, and Git discipline. Apply when proposing, planning, breaking down, or implementing a feature.
---

# Vertical Slice Delivery

Strict workflow for planning, delivering, and resuming features via vertical slices and clean Git history.

## When to Apply

Use this skill when:

- Proposing or brainstorming new features (ensure they're sliced correctly from the start)
- Planning or breaking down a project, feature, or body of work
- Splitting work into tasks (slices)
- Creating, amending, squashing, or rebasing commits
- Estimating progress on a feature branch
- Continuing after a pause or context reset

**Key trigger phrases:** "plan a feature", "break this down", "what tasks", "propose a feature", "new feature idea"

## Vertical Slice Architecture

Each slice:

- Owns a complete, end-to-end behavior (not a technical layer)
- Includes all required layers (UI, logic, data, tests)
- Maps 1:1 to a commit
- Is independently releasable to production
- Builds additively on prior slices; does not replace them
- Never sets up infrastructure or scaffolding for future slices

Think "skateboard → scooter → bicycle → motorcycle → car" not "wheels → chassis → engine → body → car".

If a slice only becomes useful after later work, it violates this rule.

## Source of Truth

- Run `git log <base-branch>..HEAD --oneline --no-decorate` before planning, committing, or measuring progress
- Auto-detect base branch via `git symbolic-ref refs/remotes/origin/HEAD | sed 's@^refs/remotes/origin/@@'`; default to `main` if detection fails
- Treat the command output as the complete list of slices; rerun before every decision, after rebases/amends, and after context resets
- Consider Git history the authoritative external memory; never assume plans exist unless reflected there

## Branch Modes

- **Bootstrap**: zero commits → create the first behavioral slice commit named by behavior, then switch to Extension
- **Extension**: existing commits represent all slices; squash into owning slices and add commits only for new behaviors

## Planning Rules

1. Split work strictly into vertical slices (no horizontal/layer-only tasks)
2. Ensure each slice is independently usable and not future-gated
3. Align one slice per Git commit

**Planning template**

```
Slice ID:
Behavior:
Includes (layers / concerns):
Commit message:
```

**Good vs Bad Slices**

Good:

```
[S1] Users can view their profile
[S2] Users can edit their display name
[S3] Users can upload a profile photo
```

Bad:

```
[S1] Create User model/migrations
[S2] Add profile API endpoints
[S3] Build profile UI components
```

The bad example creates "wheels without a vehicle"—no slice delivers user value until all are complete.

## Ownership Test

- Squash later fixes into the slice that owns the behavior
- Only create new commits when no slice already owns the behavior
- Keep Git history aligned with the evolution of the product rather than the order issues are discovered

## Scope Discipline

Leave code better than you found it (Boy Scout Rule), but only when:

- The improvement is required to support the new behavior
- The refactor reduces friction exposed by the new slice
- Without it, the new behavior would be harder to implement or maintain

Do not generalize abstractions unless the new behavior demonstrably requires it. Do not refactor for cleanliness, style, or aesthetic reasons alone.

## Commit and Squash Actions

- Extend slice: `git commit --fixup <commit-hash>`
- Ready to squash: `GIT_SEQUENCE_EDITOR=: git rebase -i --autosquash <base-branch>`
- New slice commits focus on behavior and include a stable slice identifier (e.g., `[S1] Allow user to submit a contact form`)

## Quality Check (CRITICAL)

- Switch from Author to Audit mode before each commit
- Confirm every change is the simplest solution that could survive six months in production
- Audit Checklist (MUST): evaluate each criterion, set the status to ✅/❌/N/A, supply notes, and output the table below before any commit

```
## Audit

| Criterion | Status | Notes |
|-----------|--------|-------|
| Behavioral correctness | ✅/❌ | ... |
| Edge cases handled | ✅/❌ | ... |
| No unnecessary abstraction | ✅/❌ | ... |
| No premature generalization | ✅/❌ | ... |
| Acceptable performance | ✅/❌ | ... |
| Tests assert behavior | ✅/❌/N/A | ... |
```

Fix and re-audit any failing criterion; do not commit without reporting the table.

## Human-in-the-Loop Policy

- Agent may commit, fixup, autosquash, and rebase on feature branches without approval
- Human approval required for force-pushing shared branches, merging into main/production, or explicitly high-risk/cross-cutting changes

## Stateless Operation

- Clear conversational context after completing a slice
- Recover all state from Git alone
- Use `git log <base-branch>..HEAD` and slice IDs to infer progress
- Do not rely on prior conversation history

## Feature Completion Check (CRITICAL)

1. Review the complete implementation across all slices
2. Identify realistic edge cases or integration gaps (be thorough but pragmatic)
3. Assign each issue to the slice that owns the behavior
4. Fix and fold back via `git commit --fixup <hash>`
5. Run `GIT_SEQUENCE_EDITOR=: git rebase -i --autosquash <base-branch>`
6. Emit the table below after every check, filling in each concern, owning slice, and status before declaring completion

```
## Feature Completion Check

| Concern | Owning Slice | Status |
|-------|--------------|-------------------------------|
| ... | [S1] | 🟢 Fixed / ✅ No issues found |
```

Only declare the feature complete once the table shows no outstanding concerns.

## Fallback Guidance

- If a required command fails or outputs unexpected results, pause and describe the anomaly
- Ask for clarification before proceeding
- Do not run commit or rebase actions until the issue is resolved

## Final Rule

Commit, squash, or rebase only after:

- Incremental slice validation
- Quality audit
- Explicit ownership decision

This skill ensures **vertical slice delivery, incremental progress, low context cost, and high code quality** by default.
