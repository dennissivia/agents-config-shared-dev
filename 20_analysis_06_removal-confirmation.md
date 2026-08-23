# Removal Confirmation During Planning

Removals are a planning artifact, exactly like API contracts and code shapes.
What a change deletes must be written down and agreed during planning, not
discovered by the human in the diff.

## Rule

- Whenever a plan would remove something from the project, present that removal
  for explicit approval during planning, alongside the contracts and code shapes
  the other analysis rules already require.
- State plainly what would go, why it is no longer needed, and what replaces it
  if anything does.
- Removals include files, folders, scripts, commands, script entries, published
  interfaces, configuration surfaces, and stored data.
- Ordinary editing is not a removal: rewriting a paragraph, replacing code within
  a file, or dropping lines as part of a change to that same unit. The question
  is whether something leaves the project rather than changes inside it.

## Plan Approval Is Not Execution Approval

Agreeing a removal in the plan does not authorize performing it. The destructive
operation still requires explicit human confirmation in the conversation turn
where it is about to run, per the global rule on plans and permission.

The two gates do different jobs:

- **Planning approval** catches removals the human never wanted, while changing
  them is still cheap.
- **Execution confirmation** catches the moment the removal actually happens, when
  the human can see the exact scope.

## Presenting A Removal

Give the human enough to judge it without opening the code:

- what is removed, named concretely;
- why it is no longer needed;
- what depends on it today, and what happens to those dependents;
- whether anything replaces it;
- whether it can be recovered if the decision turns out to be wrong.

If the answer to "what depends on this" is unknown, find out before proposing the
removal. A removal proposed without knowing its dependents is not ready to be
approved.

## Why

Removals are the least reversible part of a change and the hardest to review. An
addition that turns out wrong is deleted; a deletion that turns out wrong has to
be reconstructed, and is usually noticed long after the change shipped. Surfacing
removals in planning gives the human the decision at the point where it costs
nothing.

## Relationship To Other Rules

- `../global/01_global_10_plans-are-not-permission.md` — the execution-time
  confirmation this rule complements, and the reason plan approval is not enough.
- `20_analysis_04_api-contract-confirmation.md` — the same planning-gate shape,
  applied to contracts.
- `21_implementation_13_destructive-migration-approval.md` — the equivalent gate
  for schema and data removal.
