# Technical Decisions

## Trade-offs Documentation
- Capture trade-offs in commits and code comments: why simple solutions were chosen, when technical debt is acceptable, and future migration paths.
- Explain choices such as in-memory vs distributed approaches and why they fit current needs.

## Decision Records
- When a task introduces or confirms a meaningful technical decision, check whether the repository has a decision-record system such as ADRs.
- If such a system exists, record new or newly clarified decisions there rather than leaving the rationale only in chat or commit history.
- Typical candidates include architecture direction, persistence policy, API semantics, data lifecycle, testing strategy, and environment strategy.
- Do not create decision records for trivial implementation details.

## Documentation Coverage
- Before proposing a commit, ask whether the change altered:
  - a meaningful technical decision,
  - local developer setup or bootstrap steps,
  - how the system is run, tested, or validated.
- If yes, ensure the repository documentation reflects it.
- If the repository uses ADRs, put decision rationale there. If it uses READMEs or runbooks for setup and workflow, update those too.

## Example Trade-offs (patterns to consider)
- In-memory state can be acceptable at small scale when restart loss is tolerable; move to shared/distributed storage as coordination needs grow.
- Avoid cross-instance coordination until scale requires it; design a path to migrate if/when needed.
- Use configuration to tune limits per environment (e.g., lower limits in tests, higher in production).
