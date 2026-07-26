# Code Guidelines

## Priorities

When guidelines conflict, optimise in this order:

1. Correctness and required behaviour.
2. Conceptual simplicity and readability, not minimum line count.
3. Consistency with established codebase patterns.
4. Ease of future change.

## Design

- Choose the smallest conceptually complete implementation, refactoring existing code when doing so yields a simpler, more coherent design.
- Do not avoid a worthwhile refactor solely because it is substantial; minimise its blast radius by changing only the files necessary to implement and verify the improvement.
- Prefer removing unnecessary code to adding code. Do not preserve obsolete abstractions, compatibility layers, unreachable code, or structure without a current requirement.
- Use direct, explicit code and contain side effects.
- Prefer unidirectional data flow, derived values over redundant state, and explicit lifecycle dependencies.
- Avoid hidden mutation, implicit behaviour, and temporal coupling.
- Accept narrow arguments and use stable internal types rather than mutable transport or persistence models.
- Reuse established helpers when they match the required behaviour and improve consistency.
- Design for current requirements and realistic change, not hypothetical extensibility.
- Only handle edge cases supported by requirements, documented behaviour, observed usage, production evidence, or realistic failure modes.

## Naming and documentation

- Make code self-documenting through names that express business or domain intent—do not restate an operation.
- Introduce named variables to clarify conditional logic or give meaning to function results.
- Add comments only for context, constraints, assumptions, or necessary complexity that the code cannot express clearly.
- For non-obvious code, explain why it exists and what could break if it is simplified or changed.

## Abstraction and duplication

- Avoid speculative abstractions, placeholders, and single-use extractions.
- Evaluate duplication across existing and newly introduced code by shared responsibility or behaviour, not just textual or structural similarity.
- Prefer duplication until three or more total instances reveal a genuine shared concept with the same reason to change.
- Use the smallest abstraction that captures their common behaviour without coupling unrelated cases.

## Control flow and complexity

- Keep branching shallow and use guard clauses.
- Use:
  - `if` for asymmetric decisions;
  - `switch` for symmetric cases over one finite discriminant;
  - polymorphism or strategies when branches contain substantial behaviour or evolve independently.
- Treat repeated branching on the same business field as a signal to evaluate a missing abstraction.
- Normalise external data and shape checks at system boundaries.
- Avoid unnecessary algorithmic and cognitive complexity.
- Avoid O(n²) operations when collections can grow meaningfully.
- Prefer `match()` or `matchAll()` for regex handling to avoid implicit stateful iteration.

## Tests

Add or update tests when a change affects user-visible behaviour, public contracts, error handling, diagnostics, important edge cases, or regression-prone logic. Keep tests proportional to the risk.