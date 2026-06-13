# Code Guidelines

Leave touched code better than you found it.

- Fix relevant issues in files you touch.
- Avoid scope creep unless existing foundation blocks correct implementation.
- Remove obsolete or unreachable code caused by your change.
- Add tests when they reduce user-facing or diagnostic risk.

- Prefer direct, local, explicit code.
- Contain side effects; avoid hidden mutation or implicit behavior.
- Prefer unidirectional data flow.
- Prefer derived values over redundant stored state.
- Make lifecycle dependencies explicit; avoid temporal coupling.
- Prefer narrow arguments and stable internal types over mutable transport or persistence models.

- Avoid speculative abstractions, placeholders, and single-use extractions.
- Generalize only after 3+ real call sites.
- Copy-paste is acceptable until concrete patterns emerge.

- Keep branching shallow with guard clauses.
- Use `if` for asymmetric decision flows.
- Use `switch` for symmetric cases of a single finite discriminant.
- Use polymorphism or strategy objects when branches evolve independently or accumulate substantial behavior.
- Treat repeated branching on the same business field as a missing-abstraction signal.
- Normalize business-data shape checks near system boundaries.

- Keep complexity acceptable; avoid O(n²) when lists can grow.
- Prefer `match()`/`matchAll()` for regex handling; they avoid implicit stateful iteration.