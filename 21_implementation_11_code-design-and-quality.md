# Implementation Code Design and Quality

Production code is written to a design standard, not just to pass tests. This
file is the reference the self-review (`26_review_03`) uses to assess code
quality. It complements the refactoring rules (`22_refactor_01`), the
boundary-adapter rule (`21_implementation_07`), and the typing rules; it does
not replace them.

## Single Responsibility and Cohesion

- Each module, class, function, hook, or component does one thing and has one
  reason to change.
- Keep units small and focused; flag branching complexity beyond ~5 paths and
  consider decomposition.
- Group related behaviour together; do not let unrelated concerns share a unit.

## Encapsulation

- Expose intent, hide mechanism. Keep internal state and representation behind a
  small, meaningful interface.
- Do not leak internal data structures (raw maps, payloads, mutable collections)
  across a boundary; convert to typed, owned shapes at the edge.
- Prefer immutability and explicit construction over reaching into internals.

## Design Patterns — Apply, Don't Impose

- Use an established pattern when it removes real duplication or clarifies a
  responsibility (strategy, adapter, factory, result/either, state machine).
- Do not introduce a pattern, layer, or abstraction speculatively. Abstract when
  a second concrete need exists or a recorded decision demands it.
- Prefer the pattern the surrounding codebase already uses over a new one.

## Clean Architecture and Layering

- Respect dependency direction: presentation → application → domain →
  infrastructure depends inward, never outward.
- Keep transport, framework, and protocol details at the boundary; application
  and domain layers consume owned inputs and outputs.
- Business logic lives in the service/domain layer, not in controllers, routers,
  or UI components.
- One context/aggregate does not reach directly into another's internals.

## Explicitness and Error Handling

- Make absence, failure, and variants explicit in the type system (Optional /
  Result / discriminated unions), not implicit via null or thrown control flow.
- Never silently swallow errors; handle or propagate them deliberately.
- Replace magic numbers and inline literals with named constants.

## Naming and Vocabulary

- Use the codebase's ubiquitous language; names should reveal intent without a
  comment.

## Review Hook

The self-review assesses every change against this guidance under its Code
Quality, Abstractions, Clean Architecture, and DDD categories. A change that
passes tests but violates these principles is not done.
