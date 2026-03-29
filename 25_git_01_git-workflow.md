# Git/GitHub Workflow

These rules describe how to use git safely and predictably in this workflow.

## Feature Branches — Mandatory
- Never commit directly to `main` unless explicitly told to for trivial documentation updates.
- Default to feature branches for all work (e.g., `feature/<description>`, `fix/<description>`).
- The agent may choose a reasonable branch name by itself; do **not** pull the user in just to decide names.

## Commit Granularity — Per Logical Unit
- Commit when completing a **logical unit of work** (story-sized), not after every small change.
- For planned work (implementation plans, checklists): commit when completing an entire **subheading section** with all its nested tasks.
  - Example: "Project Initialization" with 5-6 sub-tasks = 1 commit when all are done.
  - Example: "Dependencies" section = 1 commit when all dependency updates are complete.
- For unplanned work or fixes: commit when the feature/fix is complete and working.
- **Before committing**, announce completion and ask: "We finished [section/feature]. Should I commit this now?"
- Keep related changes together; split unrelated changes into separate commits.

## Never Auto-Push or Auto-PR
- **Never push** commits unless user explicitly requests "push" or "create PR".
- **Never create PR** unless user explicitly requests it.
- After committing, inform user: "Committed locally. Would you like me to push or create a PR?"
- This avoids unnecessary force pushes, rebases, and squashes by allowing multiple commits to accumulate before pushing.

## Git Operations Flow
1. **Work through planned tasks** until reaching a logical completion point (e.g., entire subheading done).
2. **Announce completion**: "Finished [section name]. All [X] tasks complete. Should I commit this?"
3. **Wait for user confirmation** to proceed with commit.
4. **Stage the files**: `git add <files>` (only files related to this logical unit).
5. **Verify staged files**: Run `git status --short` and confirm all intended files are staged (status `A` or `M` in first column).
   - If files are missing or unexpected files are staged, fix staging before proceeding.
   - **CRITICAL**: Never run `git commit` without verifying staged files first.
6. **Commit**: Use structured commit template (see commit message rules).
   - **CRITICAL**: Always propose the commit message as rendered Markdown in chat first. Only construct shell commands after user approval.
   - **CRITICAL**: Always use temporary files for commit messages (see [Temporary Files](#temporary-files-for-commit-messages)).
   - Never use hardcoded paths like `/tmp/commit-msg.txt` - use `mktemp` or date-based unique names.
7. **Stop and inform**: "Committed locally. Ready to continue with next section, or would you like me to push/create a PR?"
8. **Only when explicitly instructed**:
   - Push: `git push` to current feature branch (avoid force pushes).
   - Create PR: Follow pull request rules.

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

## Pre-Work Checkpoint — Branch Size Review

Before starting a new chunk of work on an existing feature branch, the agent must check the current branch size:

```bash
git diff main..HEAD --numstat | awk '{add+=$1; del+=$2} END {printf "Branch churn: +%d -%d = %d total\n", add, del, add+del}'
```

**If total churn exceeds ~1,000 lines:**

1. **Warn the user**: "This branch already has ~N lines of churn. Before adding more work, should we turn this into a PR and start a new branch for the next slice?"
2. **Assess coherence**: Is the current branch a coherent, reviewable unit? Does it leave the app in a working state?
3. **Wait for user decision**: The user may choose to:
   - Create a PR now and start fresh (preferred)
   - Continue on the same branch (acceptable — the user overrides the soft limit)
4. **Do not block**: This is a warning, not a hard stop. Proceed if the user says to continue.

This check happens **once** when resuming or starting new work — not after every commit.

## Post-PR Merge Workflow
When the user states that they merged the PR and want to move on:

1. **Checkout main branch**: `git checkout main`
2. **Fetch remote changes**: `git fetch origin`
3. **Pull changes**: `git pull origin main`
4. **HARD STOP on conflicts**: If merge conflicts occur, stop immediately and ask the user for resolution instructions. Do not attempt to resolve conflicts automatically.
5. **Verify merge**: Ensure all changes from the feature branch are present in main
6. **Delete local feature branch**: 
   - Try `git branch -d <feature-branch>` first (safe delete)
   - If it fails and you've confirmed changes are merged, use `git branch -D <feature-branch>` (force delete)
7. **Clean slate**: You now have a clean `main` branch ready for the next feature branch

**Next steps:**
- Wait for user to describe the next task
- Create a new feature branch when starting new work
- Never continue working on a merged feature branch

## Temporary Files for Commit Messages

**CRITICAL: Use `mktemp` for commit message files to avoid file-exists errors.**

```bash
# GOOD: Safe, unique temp file
MSGFILE=$(mktemp)
cat >| "$MSGFILE" << 'EOF'
✨ Your commit message

## Summary
...
EOF
git commit -F "$MSGFILE"
unlink "$MSGFILE"
```

**Key points:**
- Always use `mktemp` or date-based unique names (`/tmp/commit-$(date +%Y%m%d-%H%M%S-%N).txt`)
- Use `>|` (not `>`) in zsh to force overwrite (zsh has `noclobber` enabled)
- Never use hardcoded paths like `/tmp/commit-msg.txt`

For complete temporary file guidelines, see `.agents/shared/dev/21_implementation_01_security.md` (Temporary Files section).

## When in Doubt — Stop
- If anything about the git state, branch, files to commit, or intended action is unclear, **do not proceed**.
- Do not guess, "auto-fix" git issues, or attempt to resolve complex conflicts alone.
- Ask the user for clarification before continuing.
