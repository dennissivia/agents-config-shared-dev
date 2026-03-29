# Implementation Plan Standards

Implementation plans are the primary artifact of the analysis phase. They translate
design documents, ADRs, and codebase analysis into a concrete, step-by-step
specification that guides the implementation phase.

A plan is not ready for implementation until it has been written to the standards below
and cross-reviewed against the project's design documents.

---

## Rule 1: Writing Standards

Implementation plans describe **what to build and why**, not how to write every line.

### Content Density

- **SQL migrations**: Include verbatim — they ARE the artifact being planned.
- **Configuration files** (YAML, XML, TOML): Include verbatim when the exact shape matters.
- **Domain models**: Field lists (name, type, nullability) and business method signatures.
  Do not include constructor bodies, getter/setter implementations, or boilerplate.
- **Service layer**: Responsibility descriptions and method signatures.
  Do not include method bodies or full implementation logic.
- **Repository interfaces**: Method signatures only.
- **DTOs / API contracts**: Field lists or compact record signatures.
  Do not include full class bodies with annotations and Javadoc.
- **Enums**: Include values — they define domain vocabulary.
- **Exceptions**: Include reason/variant lists. Do not include full class hierarchies.
- **Test plans**: Describe what to test (scenarios, edge cases, layers).
  Do not include full test method implementations.

### Guiding Principle

The diff shows the code. The plan explains the **intent, structure, and decisions**.
If a code block would be identical to what the implementer writes anyway (constructors,
boilerplate, trivial wiring), omit it. If it conveys a non-obvious decision (a specific
SQL constraint, a particular enum set, an unusual method signature), include it.

### What Belongs in a Plan

- Goal, scope, and constraints for the work
- Step-by-step breakdown of deliverables
- Domain model shapes (fields + methods, not bodies)
- API contracts and data flow
- Migration DDL (verbatim)
- Key design decisions and trade-offs
- Test plan (scenarios, not implementations)
- PR checklist / acceptance criteria

### What Does Not Belong

- Full Java/TypeScript class implementations
- Constructor bodies, builder patterns, or wiring code
- Copy-paste-ready code blocks (the plan is not a code generator)
- Implementation details that the existing codebase patterns already dictate

---

## Rule 2: Cross-Review Before Implementation

An implementation plan must be cross-reviewed before it is considered accurate and ready
for implementation. The review verifies alignment with the project's design documents
and ensures the plan does not introduce contradictions or drift.

### Review Sources

The reviewer must check the plan against:

- **Design documents** (`docs/tech/`) — domain models, architecture, API design
- **ADRs** (`docs/adr/`) — decisions that constrain implementation choices
- **Existing codebase patterns** — naming conventions, layering, established idioms
- **Other milestone plans** — cross-milestone consistency (shared types, naming, contracts)

### Review Criteria

1. **Architectural alignment**: Does the plan match the layering, packaging, and
   responsibility boundaries established in design docs?
2. **Naming consistency**: Do types, methods, enums, and concepts use the same names
   as design docs and ADRs? Flag any divergence.
3. **Content density**: Are code examples at the right level — intent-conveying, not
   implementation-dictating? Apply Rule 1 and reduce over-specified blocks.
4. **ADR compliance**: Does the plan respect recorded decisions? If it deviates, is
   the deviation intentional and documented?
5. **Cross-milestone coherence**: Are shared types, API contracts, and naming consistent
   across milestone plans that touch the same domain?

### Review Output

- Add a **Review Notes** section at the end of the plan documenting:
  - Issues found and how they were resolved
  - Decisions made during review (with rationale)
  - Any intentional deviations from design docs (with justification)
- Fix issues directly in the plan — do not leave known problems for the implementer.
- If an issue cannot be resolved without user input, flag it and pause.

### When to Review

- After the plan is written and before implementation begins.
- After significant changes to a plan that has already been reviewed.
- When design documents or ADRs are updated in ways that may affect existing plans.
