# Git/GitHub Workflow

These rules describe how to use git safely and predictably in this workflow.

## Feature Branches — Mandatory
- Never commit directly to `main` unless explicitly told to for trivial documentation updates.
- Default to feature branches for all work (e.g., `feature/<description>`, `fix/<description>`).
- The agent may choose a reasonable branch name by itself; do **not** pull the user in just to decide names.

## Never Auto-Commit
- Do not start git operations (commit, push, merge, rebase, branch creation/switching) without explicit user confirmation.
- Before any git action, present a short plan:
  - which files will be staged/committed,
  - which branch is targeted,
  - what will happen next (commit only, push, PR, etc.).

## Git Operations Flow
1. **Propose the plan**: show a concise summary of changes and list of files to be included.
2. **Ask for confirmation** before running any git command.
3. Stage only the intended files: `git add <files>`.
4. Commit using the structured commit template (see commit message rules).
5. Push to the current feature branch (avoid force pushes).
6. Create or update the PR following the pull request rules.

## Git Safety
- Never use `git push -f` / `--force` unless explicitly allowed by the user.
- Never delete tracked or untracked content unless the user explicitly requests the removal.
- If asked to remove content from git history, keep the files on disk (or ask for confirmation before deleting anything locally) and only scrub the history.
- Do not rewrite history (rebase, amend, reset) without explicit permission.
- Do not create, delete, or switch branches silently; include this in the proposed plan.
- Always verify:
  - you are on the correct branch,
  - quality gates (formatting, tests, lint) have passed before any commit or push.
- Keep commits focused: avoid mixing unrelated changes in the same commit.

## When in Doubt — Stop
- If anything about the git state, branch, files to commit, or intended action is unclear, **do not proceed**.
- Do not guess, “auto-fix” git issues, or attempt to resolve complex conflicts alone.
- Ask the user for clarification before continuing.
