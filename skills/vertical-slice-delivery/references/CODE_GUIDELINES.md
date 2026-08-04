# Code Guidelines

All rules are mandatory. Apply them together. When rules conflict, follow the priority order below.

## Priorities

1. Meet required behaviour and preserve correctness.
2. Use the fewest concepts and least indirection that remain clear and correct.
3. Minimise coupling and keep change boundaries clear.
4. Follow established codebase patterns.

## Design

- Implement the smallest complete design that satisfies current requirements.
- Refactor existing code when it reduces code, simplifies design, or establishes one source of truth.
- Remove obsolete code, compatibility layers, unreachable code, and structure without a current requirement.
- Use direct, explicit code.
- Isolate side effects.
- Always use unidirectional data flow: data down, actions up.
- Derive values instead of storing redundant state.
- Do not rely on hidden mutation, implicit behaviour, or call order; express dependencies through parameters and return values.
- Make setup, cleanup, and cancellation explicit.
- Pass only the values a function uses; do not pass broad context objects.
- Map transport and persistence models to internal types at system boundaries; do not pass mutable DTOs or ORM entities through internal logic.
- Reuse an existing helper when it implements the required behaviour.
- Implement current requirements only; do not solve hypothetical problems.
- Handle an edge case only when supported by requirements, documented behaviour, observed usage, production evidence, or realistic failure modes.

## Naming and Code Comments

- Name code after its business or domain intent, not its operation.
- Extract conditions into named booleans when they combine multiple conditions or span multiple lines.
- Assign function results to named variables before passing them to another function.
- Add code comments only for context, constraints, assumptions, or behaviour the code cannot express.
- When an invariant is not evident from the code, add a code comment explaining why it exists and what behaviour could break if it changes.

## Abstraction and Duplication

- Do not create speculative abstractions, placeholders, or single-use extractions.
- Evaluate duplication by shared responsibility or behaviour, not just textual or structural similarity.
- Do not create a shared abstraction before two instances exist; two instances trigger evaluation, not automatic abstraction.
- Create an abstraction only when instances share behaviour or responsibility and the same reason to change.
- Abstract only the shared behaviour; do not couple unrelated cases.

## Control Flow and Complexity

- Use guard clauses for invalid or exceptional cases; keep the main path flat.
- Use `if` for asymmetric decisions.
- Use `switch` for symmetric cases over one finite discriminant.
- Use polymorphism or strategies when branches implement distinct algorithms or have independent reasons to change.
- When the same business field is branched on repeatedly, evaluate a shared abstraction and apply the duplication rule before creating it.
- Always validate and normalise external data at system boundaries.
- Use the simplest algorithm and control flow that meet the requirements and expected data scale.
- Do not add abstraction, indirection, state, or branching without a concrete need.
- Do not use `O(n²)` operations on collections that are not explicitly bounded when a lower-complexity solution meets the requirements.
- Use `match()` or `matchAll()` for regular-expression handling.
- Do not use stateful regular-expression iteration that depends on mutable `lastIndex`.

## Tests

- Add or update tests only for changes affecting user-visible behaviour, public contracts, error handling, diagnostics, required edge cases, or logic with known regression risk.
- Use focused tests to cover changed behaviour and documented or reachable failure modes.