# Code Comment Policy

- Do not add detailed comments to everything you implement.
Comments are not a default part of every function, branch, variable, or code change; add them only in the required cases below.

- Create concise boundary-level documentation for every meaningful component, module, package, class, or public export.
This documentation should state the component’s purpose, responsibility, and expected outcome at a high level.

- Document public interfaces with caller-facing semantics.
Public functions, methods, classes, modules, and exported APIs must document behavior that callers need to rely on, such as purpose, side effects, idempotency, retries, caching, ordering, mutation, lifecycle, security assumptions, and failure behavior.

- Keep existing comments synchronized with code changes.
 Whenever you change code near a comment, public interface, module boundary, or documented behavior, update the comment, narrow it, or delete it in the same change so it remains true.

- Do not document what the type system already expresses.
 In Java, TypeScript, and other typed languages, do not restate parameter types, return types, visibility, inheritance, nullability, generics, unions, annotations, or obvious names.

- Document purpose and outcome, not implementation steps.
Comments should explain what the code is intended to accomplish and what stable behavior it provides, not narrate how each line, branch, or helper works internally.

- Use internal comments only for non-obvious internal behavior.
Private or internal functions should not have doc comments unless they contain a specialized algorithm, important invariant, workaround, concurrency constraint, security constraint, external system dependency, or other behavior that is difficult to infer from the code.

- Only include stable, durable information.
Do not write comments with overly specific implementation details, temporary assumptions, exhaustive error lists, internal branch descriptions, or claims that are likely to become stale unless they are enforced by tests, schemas, or public contracts.

- Treat comments as high-trust context for humans and AI agents.
A stale or incorrect comment is worse than a missing comment because it can mislead the next maintainer or agent.

- Never leave agent plans, reasoning traces, or temporary implementation notes in source comments.
Use source comments only for durable code knowledge; put workflow instructions, templates, and agent operating rules in the appropriate agent instruction files.
