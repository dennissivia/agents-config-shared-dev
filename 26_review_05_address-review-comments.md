# Addressing Review Comments

This rule defines the full workflow for triaging, fixing, committing, and closing
inline review comments left by automated reviewers (e.g., Copilot) or human reviewers.
It applies after all review comments have been loaded using the repository's
review-comment reading rules (see `26_review_02_reading-review-comments.md`).

---

## Phase 0: Trigger — When to Initiate This Workflow

This workflow **must be initiated automatically** when any of the following occur:

1. **The user mentions a review**: e.g., "Copilot reviewed the PR", "there are review
   comments", "address the review", or similar.
2. **The agent is explicitly asked** to check, assess, or address review feedback.

When triggered, the agent must:

1. **Pull all comments** (Phase 1) without waiting for further instructions.
2. **Build and present the triage table** (Phase 2) to the user.
3. **Pause for user approval** (Phase 3) before starting any fixes.

The agent must never skip the triage presentation. The human reviews the assessment
before any code changes begin.

### Retroactive Recovery If Fixes Already Started

If the agent has already started implementing review fixes before this triage
and approval step, that is a workflow violation. The agent must:

1. Stop further code changes immediately.
2. Surface the triage retroactively — pull the full comment set and present the
   triage table as if no fixes had been written yet.
3. Let the user decide whether to keep, adjust, or revert the already-applied
   local changes before any git action.

Do not commit, push, or update the review artifact for review-fix work that bypassed triage.

---

## Phase 1: Pull All Comments

Before doing anything else, fetch both the review body and all inline code comments.
Use the active repository's review-comment reading rule for the exact command.

Tool priority for review-comment workflows:

1. Use local repository scripts or helper tools specialized for the task.
2. If no local helper exists, use explicitly defined `gh` commands from the active workflow rules.
3. Use built-in tools or connectors only when no local helper or workflow-defined `gh` command
   covers the needed operation.

The loaded comment set must include the comment identifiers needed for later replies and thread
resolution.

---

## Phase 2: Triage Overview

Before touching any code, produce a prioritized triage table and present it to the user.

### Triage Table Format

| # | ID | File:Line | Comment Summary | Priority | Applicable | Proposed Fix |
|---|------|-----------|----------------|----------|------------|--------------|
| 1 | 123 | src/foo.ts:42 | Missing null check | High | Yes | Add `?? []` guard |
| 2 | 124 | src/bar.ts:10 | Rename `x` → `userId` | Low | Yes | Rename variable |
| 3 | 125 | src/baz.ts:5 | Extract helper | Style | No — one-off | Won't do |

### Priority Levels

- **High**: Correctness bugs, security issues, type safety holes, broken contracts
- **Medium**: Missing error handling, incomplete test coverage, logic smells
- **Low**: Naming, readability, minor style improvements
- **Style**: Cosmetic-only, no behavioral impact
- **Won't do**: Not applicable to this codebase, contradicts an ADR, out of scope

### Applicable Column

- **Yes**: Applies to this codebase; should be fixed
- **No**: Explain why (e.g., contradicts ADR, pattern doesn't exist here, false positive)

### Proposed Fix Column

- Brief description of the change, not full implementation
- If the fix is obvious, state it; if unclear, ask the user

---

## Phase 3: User Approval of Triage

After presenting the triage table, **pause and ask**:

1. Does the priority and applicable assessment look correct?
2. Are there any comments you want to override (escalate, downgrade, or skip)?
3. Preference: fix **one-by-one** (review each fix before the next) or **batch** (fix all applicable at once, review before committing)?

Do not start any fixes until the user acknowledges the triage table.

---

## Phase 3.5: Root Cause Analysis & Rule Proposal

**Trigger**: After the user confirms which issues to fix (end of Phase 3).
**Must complete before** any code fixing begins (Phase 4).

The purpose of this phase is to turn review feedback into lasting improvements.
Before fixing individual issues, determine whether the confirmed problems reveal
a systemic gap in the project's rules, patterns, or testing — and close that gap
first so the same class of mistake does not recur.

### Step 1: Classify Each Confirmed Issue

For each issue the user confirmed as "fix", ask:

- **Why did we miss this?** Was it a gap in agent rules, missing test coverage,
  missing code review checks, or a one-off oversight?
- **Is this generalizable?** Would a rule prevent this class of problem across
  the codebase, or is it a one-time fix?

Classify into one of:

| Category | Example | Action |
|----------|---------|--------|
| **Missing automated test** | No a11y scan runs on this page | Propose test |
| **Missing agent rule** | No rule requires `aria-label` on icon-only buttons | Propose rule |
| **Missing code review check** | Automated review doesn't flag fire-and-forget HTTP | Propose rule |
| **One-off mistake** | Typo, copy-paste error, simple oversight | No rule needed |

### Step 2: Propose Safeguards (Strict Priority Order)

For each generalizable gap, evaluate safeguards in this order. **Prefer automated
enforcement over manual rules.** A test that fails the build is stronger than a
rule that relies on the agent remembering.

#### Priority 1 — Automated Test

Can we write a test (unit, integration, or e2e) that would always catch this
class of problem automatically?

Examples:
- An e2e axe-core scan on every page (catches a11y regressions).
- A route-level test that iterates all routes and verifies auth guards.
- A lint rule or static analysis check.

If yes: propose the test. This is the preferred safeguard. A test that runs in
CI provides a permanent safety net regardless of which agent or human writes the
code.

#### Priority 2 — Shared Agent Rule

If no automated test can catch it, can we write a **shared** rule
(`shared/dev/`) that applies across projects?

Examples:
- "All HTTP calls must handle both success and failure branches explicitly."
- "Every route added must be verified against the auth guard structure."

#### Priority 3 — Local Agent Rule

If the guidance is specific to this project's stack, conventions, or libraries,
write a **local** rule (`local/`).

Examples:
- "All MUI `IconButton` components must have `aria-label`."
- "Use `Result<T, ApiError>` for all API calls; fire-and-forget is forbidden."

Local rules should also reference or produce an ADR when the decision is
architecturally significant, so the reasoning is recorded independently of
the agent rules.

#### Combining Safeguards

These levels are not mutually exclusive. A test and a rule can coexist:
- The **test** is the automated safety net (catches violations in CI).
- The **rule** is the proactive guidance (prevents violations from being written).

When both are warranted, propose both.

### Step 3: Present to User

Present the proposed safeguards as a table:

| # | Gap Identified | Safeguard Type | Proposal (summary) | Scope | Target |
|---|---------------|---------------|-------------------|-------|--------|

Pause and wait for user approval. The user may:
- Approve all proposals
- Modify scope or wording
- Reject proposals they consider unnecessary
- Defer proposals to a later session

### Step 4: Write Approved Rules and Tests

Add approved rules to the appropriate `.agents/` files.
Create approved ADRs in the `docs/adr/` directory.
Note approved tests for implementation during the fix cycle (Phase 4).

Then re-read the updated rules to ensure the fix cycle benefits from
the new guidance immediately.

### When No Systemic Gaps Exist

If all confirmed issues are one-off mistakes with no generalizable pattern,
state this explicitly and proceed to Phase 4. Do not invent rules or tests
for the sake of having output.

---

## Phase 4: Fix Cycle

### Per-Comment or Batch Fix

Apply fixes in priority order (High → Medium → Low → Style).

For each fix:

1. **Apply the code change** per the proposed fix in the triage table.
2. **Add or update a test** if the fix touches production behavior (per `24_qa_01_testing-standards.md` — regression tests are mandatory for bug fixes).
3. **Run quality gates**:
   - Frontend: `cd /path/to/frontend && npm run test:run && npm run lint`
   - Backend: `cd /path/to/backend && ./mvnw test`
4. **Present changes to user** for approval before committing.

If any quality gate fails, fix it before proceeding. Do not commit with failing tests.

### One-by-one Mode

- Fix one comment → run gates → show diff → wait for user approval → commit → continue.

### Batch Mode

- Fix all approved comments → run gates → show full diff → wait for user approval → single commit.

---

## Phase 4.5: Post-Fix Outcome And Escape Analysis

After the approved fixes are implemented and the quality gates are green, but
before any commit proposal:

1. present the fix outcome,
2. state which review comments were addressed,
3. report the gate results,
4. analyze **how these issues escaped earlier phases**,
5. suggest how to avoid the same miss in the future.

This analysis is mandatory even if a root-cause or safeguard discussion already
happened earlier in Phase 3.5. The pre-fix analysis is the hypothesis; this
post-fix step confirms what was actually missing after seeing the real fix.

### Minimum questions

- Was this primarily a missing test?
- Was this primarily a missing rule or unclear rule?
- Was this primarily a self-review miss?
- Did the QA process miss an important signal?
- Did the implementation cross a boundary that should have had stronger typing,
  cancellation, validation, or abstraction?

### Presentation format

Present a compact table:

| # | Review Item | What Escaped | Proposed Prevention | Scope |
|---|-------------|--------------|---------------------|-------|

Then summarize:

- what should be fixed now as follow-up safeguards,
- what can be deferred,
- and whether the safeguards are code/tests, rules, ADRs, or docs.

Pause for user direction before changing docs or rules.

---

## Phase 4.6: Safeguard Follow-Up

After the user reviews the post-fix escape analysis:

- apply any approved follow-up safeguards,
- rerun the scope-matching quality gates if those follow-ups change tracked files,
- present the updated outcome again if the follow-up materially changed the diff.

Typical safeguard follow-ups:

- new or expanded regression tests,
- `.agents/` rule updates,
- ADR updates,
- README or workflow documentation updates.

---

## Phase 4.7: Scope Added By The Fixes

Review fixes are meant to correct what the change already does. Sometimes they
add to it instead: a protection the change did not have, a configuration surface
it did not expose, a behaviour a reader of the pull request would not expect from
its description.

Once the approved fixes are complete and before proposing the commit, check what
the fix set added against what the pull request body claims the change delivers.

Ask, for each fix:

- Does the change now do something the body does not mention?
- Does the body now claim something the fixes made untrue, or narrower, or wider?
- Would a reviewer reading only the body be surprised by what is in the diff?

If the answer to any of these is yes, **propose the specific edit to the pull
request body and wait**. Show the current wording and the replacement, so the
difference is visible without opening the pull request.

**The human must approve every change to a pull request body.** Detecting the
need is automatic; making the edit is not. Never edit the body because the check
found something — the check produces a proposal, and the human decides whether
the wording changes and how.

When the fixes only correct behaviour the body already describes, say so in one
line and move on. Most review rounds need no change at all, and saying that
plainly is more useful than silence.

### Why

A pull request body is written before the review and describes the change as it
was then. Fixes that add scope leave it quietly wrong, and the body is what the
merge is recorded as and what anyone reads afterwards. The moment the fixes are
finished is the only point where the difference is still obvious.

---

## Phase 5: Commit

Use the standard structured commit template (see
`25_persist_02_commit-messages.md`).

Before proposing the commit:

1. ensure Phase 4.5 has been presented,
2. ensure any approved Phase 4.6 follow-ups are complete,
3. ensure the user has had a chance to confirm the final fix set.

The commit **must**:

- Reference which review comments are addressed, e.g.  
  `Addresses PR review comments: [ID-123, ID-124] — null check, variable rename`
- List addressed comment IDs in the `## Summary` or `## Key changes` section.

**Review-fix push is automatic**: once the user approves the commit message, push immediately without asking again. The "Committed locally. Would you like me to push?" pattern does **not** apply to review-fix commits. After the commit message is approved:

1. Push the branch.
2. Proceed directly to Phase 6 (reply to comments + resolve threads).
3. Then continue the review loop according to repository review-detection rules.

No additional user handover is needed between commit approval and poll completion.

---

## Phase 6: Close Addressed Comments

After the user approves the commit message and git push has happened, reply to
every fixed comment and resolve threads.

### Reply Messages

- **Fixed**: `Fixed in <short-hash>`
- **Won't fix**: `Won't fix`

No justification, description, or internal reasoning in replies. Keep them minimal.

Get the short hash with `git rev-parse --short HEAD`.

### Reply Command

Use the active repository's review-comment follow-up rule for the exact reply command. Prefer
local scripts or helper tools when they exist. If the repository does not define a helper, use the
workflow-defined GitHub command that replies to the existing review comment identifier.

### Resolve Threads via GraphQL

Use the active repository's review-comment follow-up rule for thread resolution. The follow-up tool
must resolve the review thread when that operation is supported. If thread resolution is not
available in the current environment, the reply alone is sufficient and the human reviewer can close
threads in the GitHub UI.

---

## Phase 7: "Won't Do" Comments

For every comment marked **Won't do / Not applicable** in the triage table:

1. Post a reply via the same command used in Phase 6, with body `Won't fix`.
2. No justification or internal reasoning in the reply.
3. This is done **after all fixes are committed**, in a single pass.

---

## Summary Checklist

- [ ] Pulled all inline comments and review body  
- [ ] Triage table presented and approved by user  
- [ ] User preference (one-by-one / batch) confirmed  
- [ ] All High + Medium applicable comments fixed  
- [ ] Quality gates pass (tests, lint, format)  
- [ ] User approved diff before commit  
- [ ] Scope added by the fixes checked against the pull request body, and any
      edit proposed to the human and approved before it is made
- [ ] Commit references addressed comment IDs  
- [ ] REST reply posted on each fixed comment (`Fixed in <hash>`)  
- [ ] Thread resolution attempted (GraphQL or noting fallback)  
- [ ] "Won't do" comments replied to with explanation  
