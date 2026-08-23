# Data Type, Nullability, and Schema Review (Planning Phase)

Type choices, API contract field shapes, and database schema fields are among the
most impactful and hardest-to-change decisions in a change. They must be reviewed
and decided during planning, not discovered during implementation or code review.

## Rule

For every API contract field and every database field that a change adds or
modifies, perform an explicit data-type review during the planning phase. The
review decides, per field:

- the data type — prefer a bounded/domain type over a raw primitive where it
  conveys meaning;
- nullability — whether the value can be absent/null, and the reason;
- constraints — required vs optional, ranges, enums, uniqueness, defaults;
- consistent expression of nullability across the stack: the domain/DTO type, the
  generated/published API contract (mark the field nullable so the contract is
  accurate for generated clients), and the database column.

Record the outcome in the plan or agent plan, and confirm API contract shapes with
the human per the API-contract-confirmation rule. Do not defer these decisions to
implementation.

## Why

- Nullability and type mismatches between the code, the published API contract,
  and the database schema are a recurring source of defects and broken generated
  clients. They are cheap to decide in planning and expensive to change once
  clients and data depend on them.
- The published contract and schema are consumed by other systems and reviewers,
  so an inaccurate type or nullability declaration misleads them even when the
  application code is correct.

## Relationship to other rules

- `20_analysis_04_api-contract-confirmation.md` confirms request/response shapes
  with the human; this rule adds the per-field type and nullability decision that
  the shape confirmation depends on.
- `21_implementation_10_database-migration-safety.md` governs safe schema changes;
  this rule ensures each field's type and nullability are decided before the
  migration is written.
