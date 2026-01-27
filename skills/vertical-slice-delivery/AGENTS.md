# Vertical Slice Delivery

- Git history is the only source of truth.
- Before planning, committing, rebasing, or resuming work, run `git log <base-branch>..HEAD --oneline --no-decorate`.
- Detect base branch via `git symbolic-ref refs/remotes/origin/HEAD | sed 's@^refs/remotes/origin/@@'`; default to `main`.
- Exactly one vertical slice per commit.
- A commit owns the behavior it introduces.
- Only create a new commit if no existing commit owns the behavior.
- Later fixes and extensions must be squashed into the owning commit.
- Do not start a new slice until the current slice is committed unless explicitly waived.
- Bootstrap mode: no slice commits exist → first commit must deliver a complete behavior.
- Extension mode: existing commits define all slices; new commits represent new behaviors only.
- Always check `git status` before committing.
- Commit only the files you touched and list each path explicitly.
- For new files, use `git restore --staged :/ && git add "path/to/file1" "path/to/file2" && git commit -m "<scoped message>" -- path/to/file1 path/to/file2`.
- Quote any git paths containing brackets or parentheses (e.g. `src/app/[candidate]/**`) when staging or committing so the shell does not interpret them.
- Commit messages must include a stable slice identifier (e.g. `[S1]`).
- Extend slices using `git commit --fixup <commit-hash>`.
- Autosquash using `git rebase -i --autosquash <base-branch>`.
- When running `git rebase`, avoid opening editors—export `GIT_EDITOR=:` and `GIT_SEQUENCE_EDITOR=:` or pass `--no-edit`.
- Leave code better than you found it (Boy Scout Rule) but only when required to support the current slice.
- Do not generalize, future-proof, or introduce abstractions without immediate behavioral need.
- Before every commit, review your changes against the following criteria and output the audit table below; do not commit if any required item fails:
  ```
  ## Audit

  | Criterion | Status | Notes |
  |-----------|--------|-------|
  | Behavioral correctness | ✅/❌ | ... |
  | Gaps and edge cases handled | ✅/❌ | ... |
  | No unnecessary abstraction | ✅/❌ | ... |
  | No premature generalization | ✅/❌ | ... |
  | Acceptable performance | ✅/❌ | ... |
  | Tests assert behavior | ✅/❌/N/A | ... |
  ```
- After each slice, discard conversational context.
- Recover all state from Git history only.
- When feature complete, perform a completion check:
  - Audit all slices as a whole using Audit table criterion
  - Assign each concern to an owning slice
  - Fix via `git commit --fixup`
  - Autosquash and output the table below:
    ```
    ## Feature Completion Check

    | Concern | Owning Slice | Status |
    |--------|--------------|--------|
    | ... | [S1] | 🟢 Fixed / ✅ No issues |
    ```
- If a required command fails or output is unexpected, stop and ask.