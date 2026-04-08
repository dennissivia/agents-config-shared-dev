
---

## 🧩 `26_pr_01_pull-requests.md` (updated)

```md
# Pull Requests

Pull requests are the final narrative of a change set. They should be cohesive, reviewable, and aligned with the commit history.

## PR Size — Soft Limits

PRs must be reviewable. Large PRs degrade review quality, obscure intent, and lose commit narrative on squash merge.

### Soft limit: ~2,000 lines of total churn

- **Churn** = insertions + deletions (the `git diff --stat` total).
- Tests typically dominate churn (50–60%). That's expected and healthy — test code is scannable. The real review burden is production code.
- Seed data, migrations, and docs are low-signal churn — they inflate numbers but don't add review complexity.
- When a PR exceeds ~2,000 lines, it's a signal to ask: "Could this have been two vertical slices?"

### Vertical slices

A **vertical slice** is a coherent, deployable unit of work that cuts across layers:

- Backend: API contract + service + persistence + tests
- Frontend: page/component + hook + API client + tests
- Supporting: migration, seed data, docs as needed

Each slice should be independently mergeable and leave the application in a working state. A slice does not need to deliver the complete feature — it delivers one usable piece.

**Examples of good slices for a "Checklists" feature:**
- PR 1: Checklist CRUD API + backend tests
- PR 2: Checklists overview page + frontend tests
- PR 3: Checklist items (add/edit/complete/delete) across both stacks
- PR 4: Seed data + E2E tests + QA cleanup

Each stays under ~2,000 lines. Each is reviewable in one sitting.

### Squash merge context

This project uses **squash merge** for PRs. Every PR becomes a single commit on `main`. This means:

- Individual commit messages within a PR are lost on merge.
- The PR description becomes the permanent record.
- Prefer **fewer, well-scoped commits per PR** (1–3 is ideal).
- The investment in high-quality commit messages pays off most when each commit is its own PR.

### When to exceed the limit

Exceeding ~2,000 lines is acceptable when:
- Splitting would create broken intermediate states.
- The churn is dominated by generated code, migrations, or seed data.
- The user explicitly decides to ship a larger PR.

The limit is a **checkpoint**, not a blocker. The agent must surface it; the user decides.

## Creating PRs
- Prefer the GitHub CLI (`gh`) for GitHub interactions such as PR creation, PR editing, and repo metadata lookup unless the user explicitly wants a different tool.
- When the user explicitly approves the publish flow, create a **normal PR by default**.
- Use a **draft PR** only when:
  - the user explicitly asks for draft,
  - the branch is intentionally incomplete,
  - or there is a known blocker the user wants visible before final review.
- **Single-commit PR**:
  - Use `gh pr create --fill` to auto-populate from the commit message.
- **Multi-commit PR**:
  - Do **not** use `--fill` (it only uses the last commit).
  - Manually craft the PR body using `gh pr edit <number> --body "..."`, mirroring the structured commit template.

## PR Content
- Keep the PR narrative cohesive across all commits:
  - What problem are we solving?
  - What is the overall solution?
  - What are the key trade-offs or risks?
- For mixed user-facing and technical changes:
  - Center the PR on the user-facing feature.
  - List supporting technical work under an “Additional changes” or similar section.

## Preconditions Before Opening a PR
- Quality gates have been run and are passing:
  - formatter/linter
  - tests (unit, integration, end-to-end where applicable)
- The branch contains only intended changes; no stray or debug files.
- Commit messages follow the structured template and correctly describe the work.
- The branch is based on the appropriate target branch (typically `main`) and up to date as agreed for the project.

## Safety Rules
- Do not open a PR with known failing tests or lint issues unless the PR is explicitly about fixing them—and call this out clearly.
- Do not force push (`--force`) or perform history rewrites on the PR branch unless explicitly instructed by the user.
- If CI fails:
  - Investigate and summarize the failure.
  - Fix it if it is clearly related to your changes.
  - If it appears unrelated or environment-specific, highlight this and ask how to proceed.

## Keeping PRs in Sync
- Ensure the PR body reflects the **final** state of the branch, not an earlier intermediate state:
  - Update the PR description if the approach changes during development.
  - Remove references to experiments or directions that were abandoned.
- When adding new commits that change behavior, re-check whether the PR description needs updating.

## Communication
- After creating or updating a PR, share the PR link and a short summary:
  - what it does,
  - what needs review,
  - anything that reviewers should pay special attention to (e.g., risky areas, migrations).
- Be explicit about what has been tested and what is still uncertain.
