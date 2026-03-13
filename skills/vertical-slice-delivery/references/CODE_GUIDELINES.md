# Code Guidelines

Leave code better than you found it:

- Fix issues in files you touch for this slice.
- Avoid scope creep unless the existing foundation blocks the correct implementation.
- Contain side effects; avoid hidden mutations or implicit behavior.
- Remove obsolete/unreachable code caused by the change.
- Always prefer direct, local, explicit code over indirection.
- Avoid speculative abstractions and placeholders.
- Do not introduce single-use extractions or abstractions unless it enables concrete reuse, or is lint-driven.
- Extract/generalize when there are 3+ real call sites (counting both existing and new code).
- Copy-paste is acceptable until concrete patterns emerge.
- Prefer unidirectional dependency/data flow.
- Prefer derived values over redundant stored state.
- Ensure acceptable algorithmic complexity (prefer O(n) over O(n²) when lists can grow).
- Add tests where they reduce meaningful user-facing or diagnostic risk.
