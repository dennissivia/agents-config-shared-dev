# Addressing PR Review Comments

This rule defines the full workflow for triaging, fixing, committing, and closing
inline review comments left by automated reviewers (e.g., Copilot) or human reviewers.
It applies after all review comments have been pulled using the commands in
`26_pr_02_reading-pr-review-comments.md`.

---

## Phase 0: Trigger — When to Initiate This Workflow

This workflow **must be initiated automatically** when any of the following occur:

1. **The user mentions a review**: e.g., "Copilot reviewed the PR", "there are review
   comments", "address the review", or similar.
2. **The agent is explicitly asked** to check, assess, or address PR feedback.

When triggered, the agent must:

1. **Pull all comments** (Phase 1) without waiting for further instructions.
2. **Build and present the triage table** (Phase 2) to the user.
3. **Pause for user approval** (Phase 3) before starting any fixes.

The agent must never skip the triage presentation. The human reviews the assessment
before any code changes begin.

---

## Phase 1: Pull All Comments

Before doing anything else, fetch both the review body and all inline code comments:

```bash
# Review summary (Copilot or any reviewer)
gh api repos/{owner}/{repo}/pulls/{pr_number}/reviews \
  --jq '.[] | "---\nReviewer: \(.user.login)\n\(.body)\n"' | cat

# Inline code comments (with IDs for later closing)
gh api repos/{owner}/{repo}/pulls/{pr_number}/comments \
  --jq '.[] | "---\nID: \(.id)\nFile: \(.path):\(.line)\n\(.body)\n"' | cat
```

Always pipe through `| cat` to prevent interactive pager sessions.

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

## Phase 5: Commit

Use the standard structured commit template (see `25_git_02_commit-messages.md`).

The commit **must**:

- Reference which review comments are addressed, e.g.  
  `Addresses PR review comments: [ID-123, ID-124] — null check, variable rename`
- List addressed comment IDs in the `## Summary` or `## Key changes` section.

**Do not push** unless the user explicitly asks.

---

## Phase 6: Close Addressed Comments

After each commit, reply to every comment that was fixed and mark threads as resolved.

### Reply to Each Comment

```bash
gh api repos/{owner}/{repo}/pulls/{pr_number}/comments/{comment_id}/replies \
  --method POST \
  --field body="Fixed in <short-hash>" | cat
```

Get the short hash with `git rev-parse --short HEAD`.

### Resolve the Thread

GitHub does not expose thread resolution via the REST API for review comments. Use the
GitHub GraphQL API:

```bash
gh api graphql -f query='
mutation {
  resolveReviewThread(input: { threadId: "PRT_..." }) {
    thread { isResolved }
  }
}
'
```

**Practical note**: Thread IDs (`PRT_...`) are not returned by the REST comments
endpoint. They require a separate GraphQL query:

```bash
gh api graphql -f query='
{
  repository(owner: "{owner}", name: "{repo}") {
    pullRequest(number: {pr_number}) {
      reviewThreads(first: 50) {
        nodes {
          id
          isResolved
          comments(first: 1) {
            nodes { databaseId body path line }
          }
        }
      }
    }
  }
}
' | cat
```

Match `databaseId` from GraphQL to the REST comment `id` to find the thread ID.

**Fallback**: If GraphQL resolution is impractical, replying with `Fixed in <hash>` on
the REST comment is sufficient to communicate resolution. The human reviewer can close
the thread in the GitHub UI.

---

## Phase 7: "Won't Do" Comments

For every comment marked **Won't do / Not applicable** in the triage table:

1. Post a brief reply explaining why via REST:
   ```bash
   gh api repos/{owner}/{repo}/pulls/{pr_number}/comments/{comment_id}/replies \
     --method POST \
     --field body="Won't fix: <reason — e.g., contradicts ADR-0008, or: Enum approach is intentional per project conventions>" | cat
   ```
2. This is done **after all fixes are committed**, in a single pass.

---

## Summary Checklist

- [ ] Pulled all inline comments and review body  
- [ ] Triage table presented and approved by user  
- [ ] User preference (one-by-one / batch) confirmed  
- [ ] All High + Medium applicable comments fixed  
- [ ] Quality gates pass (tests, lint, format)  
- [ ] User approved diff before commit  
- [ ] Commit references addressed comment IDs  
- [ ] REST reply posted on each fixed comment (`Fixed in <hash>`)  
- [ ] Thread resolution attempted (GraphQL or noting fallback)  
- [ ] "Won't do" comments replied to with explanation  
