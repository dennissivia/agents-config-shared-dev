# API Contract Confirmation Before Implementation

API contracts — the request/response payload shapes, field and collection names,
status codes, and error shapes for new or changed endpoints — are a planning
artifact, not an implementation detail. They must be written and confirmed by a
human before the implementation that depends on them begins.

## Rule

- Whenever an increment or task introduces or changes an API endpoint's request
  or response shape, write the contract during planning/preparation, not during
  implementation.
- Present the contract — the concrete payload (example JSON and/or the typed
  record/DTO field list with names, types, nullability), status codes, and the
  error shape — for explicit human review.
- This is a hard handoff: pause and wait for the human to confirm or adjust the
  contract before writing implementation code against it.
- Confirm the contract at the **start of the increment plan** when the endpoint
  is known at that point; otherwise **at the latest at the start of task-plan
  creation** for the task that implements it. Never defer contract confirmation
  to implementation.

## Why

- Field names, collection naming, envelope shape, and error semantics are
  product and architecture decisions the human owns. Discovering or changing them
  mid-implementation causes rework across both sides of the stack and can break
  wire compatibility with already-deployed clients.
- A confirmed contract lets frontend and backend proceed against a stable, agreed
  shape, and lets reviewers check the implementation against an agreed reference.

## What to present

- The endpoint(s) and method.
- The success payload as concrete JSON and/or the typed field list.
- Names for any grouped or split collections — avoid ambiguous bare names that
  would collide if the response later grows.
- Status codes and the error/failure shape.
- Any deviation from the project's established envelope conventions, called out
  explicitly.

## Relationship to other rules

- The increment-kickoff protocol (`20_analysis_03`) is where the contract is first
  surfaced for new endpoints; this rule makes the human-confirmation gate
  explicit and mandatory.
- The implementation-plan standards (`20_analysis_02`) govern how the confirmed
  contract is written into the plan.
- When the contract reflects a durable architectural choice, record it as an ADR
  per the decision-record rules.
