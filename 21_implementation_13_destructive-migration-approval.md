This rule governs migrations that can lose data — any `DELETE`, `TRUNCATE`,
`DROP COLUMN`/`DROP TABLE`, or type change that discards or rewrites existing rows.
It applies in addition to `21_implementation_10_database-migration-safety.md`, which
covers how to express schema changes safely; this rule governs the review and
approval gate before such a migration is written or applied.

## Rule

A destructive migration is a HARD STOP. Before writing or applying it, the agent
must validate its safety, produce a written summary, and obtain explicit human
approval. Never proceed on assumption.

### Always validate

- **No unintended data loss.** Confirm that no data is lost, or that every loss is
  explicitly intended and named. Where the target environment can be inspected,
  verify against the actual data (for example, count the rows the operation would
  affect) rather than assuming — at all times, not only when loss seems likely.
- **Only the intended records are affected.** Scope every destructive operation to
  exactly its target set. An unqualified statement, or a broad
  `DELETE ... WHERE id IN (SELECT ...)` that could reach unintended rows, is not
  acceptable; narrow it and prove the scope.

### Always summarize for the human

Present a clear, plain-language summary before approval, stating:

- what the schema change does,
- which records are affected and why,
- which records are explicitly **not** affected and why they are safe,
- how the above was validated, including any data check performed.

### Hard approval

- The human must explicitly confirm the destructive migration before it is written
  or applied. This is a hard approval gate, not a notification, and it holds even
  when the change looks obviously safe.
- Record the confirmed safety summary where the change is reviewed (commit body or
  pull-request body) so reviewers can see the reasoning. Its absence reads as
  unsafe — a reviewer who cannot see why a destructive change is safe must assume
  the worst.

## Why

Destructive migrations act on data that cannot be recreated. An unscoped delete or
an unstated assumption can remove real records silently, and a reviewer without the
safety reasoning has to assume the worst. A validated, written, human-approved
summary makes the change auditable and safe, and keeps the decision with the human
who owns the data.

## Relationship to other rules

- `21_implementation_10_database-migration-safety.md` — how to express schema
  changes safely (`ALTER` over `DROP`+`ADD`, incompatible type-change handling).
  This rule adds the mandatory safety summary and the hard human-approval gate for
  destructive migrations.
