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
