---
name: vertical-slice-delivery
description: Enforces vertical slice planning, incremental delivery, and Git commit discipline on feature branches. Use when planning features, breaking down work into tasks, making commits, or continuing work after a context reset.
---

# Vertical Slice Delivery

This skill defines a **strict, LLM-native workflow** for planning, implementing, and delivering software using **Vertical Slice Architecture**.

It governs how work is:

- Sliced
- Sequenced
- Committed
- Resumed after context resets

The goal is fast, high-quality delivery with minimal context and clean Git history.

---

## When This Skill Applies

This skill applies whenever **either the user or the agent itself** is about to:

- Plan or break down a project, feature, or body of work
- Split work into tasks or slices
- Create, amend, squash, or rebase commits
- Determine progress on a feature branch
- Continue work after a pause or context reset

If the agent determines that any of these actions are required it should apply this skill automatically and bind itself to its rules.

---

## Vertical Slice Architecture

Vertical Slice Architecture structures work around **complete behaviors**, not technical layers.

Each vertical slice:

- Owns an end-to-end capability
- Includes all required layers (UI, logic, data, tests)
- Corresponds to exactly one Git commit
- Represents a releasable increment of the product

---

## Incremental, Releasable Progress (Critical Constraint)

All planning and slicing must follow an **additive delivery model**.

Rules:

- Every slice must be independently useful and releasable
- Each slice must build on previous slices, not replace them
- Do not plan slices that only become useful after later work
- Do not create placeholder, scaffolding-only, or throwaway implementations

Slices must resemble:

- Skateboard → Scooter → Bicycle → Motorcycle → Car

Not:

- Wheels → Chassis → Engine → Body → Car

If a slice resembles “wheels without a vehicle”, it violates this skill.

---

## Source of Truth (Non-Negotiable)

Before planning, committing, or determining progress, **always run**:

`git log <base-branch>..HEAD --oneline --no-decorate`

### Detecting the base branch

If the user hasn't specified a base branch, detect it automatically:

```bash
git symbolic-ref refs/remotes/origin/HEAD | sed 's@^refs/remotes/origin/@@'
```

If detection fails (no remote configured), fall back to `main`.

Rules:
- The output is the complete and authoritative list of commits on the current branch not in the base branch
- Each commit represents an existing vertical slice
- Only commits in this list may be referenced, amended, or squashed into
- Re-run this command:
  - Before every commit decision
  - After any amend, rebase, or autosquash
  - After any context reset

Git history is the authoritative external memory.

The agent must not assume that prior plans exist unless they are reflected in Git commits.

---

## Branch Modes

### Bootstrap Mode (New Branch)

If `git log <base-branch>..HEAD` returns no commits:

1. Treat the branch as having zero existing slices
2. Create a new commit for the first behavioral vertical slice
3. Name the commit by behavior
4. After the first commit exists, switch to Extension Mode

### Extension Mode (Existing Branch)

If `git log <base-branch>..HEAD` returns commits:

- Those commits represent all existing slices
- Never reference or amend commits not in this list
- Prefer squashing into an existing slice
- Create a new commit only for a new, independent behavioral slice

---

## Planning Rules (STRICT)

When planning work:

1. Split work only into vertical slices
2. Each slice must be independently usable
3. No horizontal or layer-based tasks
4. No future-dependent slices
5. Each slice maps 1:1 to a Git commit

Required planning output:

- Slice ID:
- Behavior:
- Includes (layers / concerns):
- Commit message:

Slice IDs should be stable to support stateless recovery.

### Example Slice Output

```text
Slice ID: [S1]
Behavior: User can submit a contact form
Includes (layers / concerns): UI, API, database, tests
Commit message: [S1] Allow users to submit a contact form
```

### Good vs Bad Slicing

**Good (vertical, each slice is usable):**
```text
[S1] User can view their profile page
[S2] User can edit their display name
[S3] User can upload a profile photo
```

**Bad (horizontal, layers without behavior):**
```text
[S1] Create User model and migrations
[S2] Add profile API endpoints
[S3] Build profile UI components
```

The bad example creates "wheels without a vehicle"—none of the slices deliver user value until all are complete.

---

## Ownership Test

When a missed edge case or necessary improvement is discovered later, squash that change back into the slice that owns the behavior. This keeps Git history aligned with the actual evolution of the product rather than the order in which issues were discovered.

Only create a new commit if no existing slice fully owns the behavior.

### Boy Scout Rule (Constrained)

This skill follows a constrained form of the _Boy Scout Rule_:
“Leave the code better than you found it.”

In this workflow, “better” is strictly defined as:

- Improvements that are required to support the new slice’s behavior
- Refactors that reduce friction exposed by the new slice
- Performance or structural changes made necessary by the new behavior

Rules:

- Improvements must directly enable or simplify the new slice
- Do not refactor solely for cleanliness, style, or aesthetic improvement
- Do not generalize abstractions unless the new slice demonstrably requires it
- Prefer the smallest possible improvement that supports the new behavior
- All such changes are owned by the new slice’s commit

If an improvement is not required to deliver the new behavior, it does not belong in the slice.

---

## Commit and Squash Actions

When extending an existing slice:
`git commit --fixup <commit-hash>`

When ready to squash fixups:
`GIT_SEQUENCE_EDITOR=: git rebase -i --autosquash <base-branch>`

Note: The `GIT_SEQUENCE_EDITOR=:` prefix auto-accepts the rebase todo list, avoiding interactive prompts.

When creating a new slice:

- Use a behavior-focused commit message
- Avoid implementation detail
- Prefer stable slice identifiers such as [S1], [S2]

---

## Required Decision Report

Before committing, output:

Decision:

- Target commit: <hash | NEW>
- Reason:
- Behavior owned:
- Git action:

If uncertain, default to amending.

---

## Quality Check

Before committing, explicitly switch from **Author mode** to **Audit mode** and output the following report. Commits without a preceding audit report violate this skill.

All code should be the simplest solution that could plausibly survive 6 months in production.

### Required Audit Report

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

If any criterion fails, fix the issues and re-audit before committing.

---

## Human-in-the-Loop Policy

By default, this workflow does **not** require human approval.

The agent may:

- Commit, fixup, autosquash, and rebase on feature branches

Human approval is required only for:

- Force-pushing shared branches
- Merging into main or production
- Explicitly high-risk or cross-cutting changes

---

## Stateless Operation and Context Reset

This workflow is explicitly designed to support stateless execution.

The agent may:

- Clear conversational context after completing a slice
- Restart or be replaced by another agent
- Recover all state from Git alone

State reconstruction rules:

- Use `git log <base-branch>..HEAD` to determine completed slices
- Infer progress from commit messages and slice identifiers
- Do not rely on prior conversation history

---

## Feature Completion Check

When all planned slices are complete, perform a final sweep across the entire feature before considering it done.

1. Review the feature holistically for missed edge cases, integration issues, or gaps between slices
2. For each issue found, identify which existing slice owns the behavior
3. Fix and fold back into the owning slice using `git commit --fixup <hash>`
4. Run `GIT_SEQUENCE_EDITOR=: git rebase -i --autosquash <base-branch>` to squash

Focus on **likely, realistic edge cases** that users would plausibly encounter. Do not exhaustively hunt for niche scenarios — rely on error reporting for those. Keep it pragmatic.

### Required Completion Report

```
## Feature Completion Check

| Issue | Owning Slice | Status |
|-------|--------------|--------|
| ... | [S1] | ✅ Fixed / ✅ None found |
```

Only mark the feature complete when no issues remain.

---

## Fallback / Error Guidance

If a required command cannot be executed or returns unexpected output:

- Stop and describe the anomaly
- Ask for clarification if needed
- Do not proceed with commit or rebase actions until resolved

---

## Final Rule

Only after:

- Incremental slice validation
- Quality check
- Explicit ownership decision

…should the agent commit, squash, or rebase.

This skill makes **vertical slice delivery, incremental progress, low context cost, and high code quality the default**.
