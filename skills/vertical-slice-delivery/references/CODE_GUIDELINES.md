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
- Only use `if` statements for asymmetric decision flows where conditions differ in meaning, priority, or control flow.
- Only use `switch` when branching on a single finite discriminant with symmetric peer cases.
- Prefer polymorphism or strategy objects when branches evolve independently or accumulate substantial behavior.
- Keep branching shallow by using guard clauses and early returns instead of deeply nested condition trees.
- Treat repeated branching on the same business field across multiple modules as a potential missing abstraction.
- Isolate business-data shape checks near system boundaries and normalize them into stable internal representations.
- Do not pass large business objects through branching logic when only a few fields are needed; extract or normalize the required values near the boundary.
- Prefer narrow function arguments and stable internal types over passing mutable transport or persistence models throughout the codebase.
- Add tests where they reduce meaningful user-facing or diagnostic risk.
