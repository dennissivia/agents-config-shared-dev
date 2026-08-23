# Touched-File Review: Comments, Refactoring, Types, and Contracts

Extends the mandatory self-review (`26_review_03_mandatory-code-review-phase.md`).
During the self-review phase, in addition to the code- and test-quality categories
defined there, assess **every file the change touched** against the checks below
and report findings with the same BLOCKER / IMPORTANT / SUGGESTION severities.

## a) Obsolete comments in touched files

For each changed production and test file, review its comments — including
pre-existing ones near the change — and remove or correct any that no longer match
the code (see `21_implementation_12_code_comments.md`, "keep comments
synchronized"). A stale comment is worse than no comment.

## b) Unnecessary comments in touched files

Remove comments the code-comment policy would not call for, including **older
comments written before that policy** — for example comments that restate the type
system, narrate implementation steps line by line, or state the obvious. Do not
preserve a comment merely because it predates the rule.

## c) Refactoring opportunities in touched production files

Assess changed files for extractable duplication:

- Consider a refactoring only when the same logic appears **more than twice** —
  identically or with slight variance — somewhere reachable from the change.
- Below that threshold, leave the code inline; do not abstract speculatively
  (`22_refactor_01_code-quality.md`).

## d) Refactoring opportunities in touched test files

Apply the same "more than twice" duplication check to changed **test files** —
shared setup, fixtures, stubbing/mocking, and assertion patterns — and extract into
shared helpers only when the threshold is met (`24_qa_06_test-implementation-quality.md`).

## e) Types, nullability, and type safety

Review the types introduced or changed on the **frontend, backend, and in API
contracts**:

- Confirm they follow the project's type-safety and coding guidelines (no unsafe
  escape hatches, explicit absence/failure, exhaustive handling).
- Check nullability explicitly: a value that can be absent must be modelled as
  nullable in its type and declared nullable in any published contract.

## f) API contract vs. persistence compatibility

API contracts are not 1:1 with database schemas, but where they overlap they must
be consistent:

- For any property in an API **write** payload that also exists as a persisted
  field, confirm the type and nullability are compatible and correctly declared on
  both sides.
- Flag mismatches — for example a non-null contract field mapped to a nullable
  column, or vice versa — per
  `20_analysis_05_data-type-and-nullability-review.md`.

## Relationship to other rules

- `26_review_03_mandatory-code-review-phase.md` — the base self-review this extends.
- `21_implementation_12_code_comments.md` — the comment policy the comment checks
  enforce.
- `22_refactor_01_code-quality.md` — the refactoring/duplication guidance behind the
  "more than twice" threshold.
- `20_analysis_04_api-contract-confirmation.md` and
  `20_analysis_05_data-type-and-nullability-review.md` — the contract and
  type/nullability decisions this review verifies were honoured.
