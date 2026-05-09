# Git/GitHub Workflow

These rules describe how to use git safely and predictably in this workflow.

## Feature Branches — Mandatory
- Never commit directly to `main` unless explicitly told to for trivial documentation updates.
- Default to feature branches for all work (e.g., `feature/<description>`, `fix/<description>`).
- The agent may choose a reasonable branch name by itself; do **not** pull the user in just to decide names.

### Branch Naming Style

- Prefer branch names that describe the **outcome or capability**, not just the
  milestone or project-management label.
- Good branch names help reviewers understand the purpose without opening the diff.
- Milestone context is acceptable only when it adds useful clarity.

Good:

- `feature/passkey-auth-ui`
- `feature/onboarding-exit-paths`
- `tech/test-data-builder-cleanup`

Acceptable when milestone context genuinely helps:

- `feature/m3-2-passkey-auth-ui`

Avoid names that are mostly tracking labels without a clear outcome:

- `feature/m3-2`
- `work/increment-5`
- `feature/task-group-7`

Use safe branch-name characters:

- lowercase letters
- numbers
- hyphens
- optional `/` for one level of grouping

Avoid special characters that require shell escaping.

## Commit Granularity — Per Logical Unit
- Commit when completing a **logical unit of work** (story-sized), not after every small change.
- For planned work (implementation plans, checklists): commit when completing an entire **subheading section** with all its nested tasks.
  - Example: "Project Initialization" with 5-6 sub-tasks = 1 commit when all are done.
  - Example: "Dependencies" section = 1 commit when all dependency updates are complete.
- For unplanned work or fixes: commit when the feature/fix is complete and working.
- **Before committing**, announce completion and ask: "We finished [section/feature]. Should I commit this now?"
- Keep related changes together; split unrelated changes into separate commits.

## Version Maintenance For Versioned Increments

- When work belongs to a named release or increment with an explicit version string
  (for example `V0.7`, `v1.2`, or a similarly versioned release branch/plan),
  treat frontend/backend version metadata as part of the release artifact rather
  than as incidental package metadata.
- On the **last PR of that increment**, before the final commit/PR handoff, update
  the version for each application surface changed by the increment:
  - frontend: `frontend/package.json`
  - backend: `backend/pom.xml`
- Do not bump versions at the start of the increment or on intermediate PRs unless
  the user explicitly wants earlier version visibility.
- Only bump the surfaces actually touched by the increment. If the increment is
  backend-only, do not bump the frontend just for symmetry.
- The agent must call out the planned version bump in the pre-commit / pre-PR
  handoff for the final PR of the increment so the user can verify the target
  version before git actions.

## Never Auto-Push, Auto-PR, or Auto-Merge
- **Never push** commits unless user explicitly requests "push" or "create PR".
- **Never create PR** unless user explicitly requests it.
- **Never merge a PR** — merging is always a human action.
- After committing, inform user: "Committed locally. Would you like me to push or create a PR?"
- This avoids unnecessary force pushes, rebases, and squashes by allowing multiple commits to accumulate before pushing.

**Exception — review-fix commits**: When the commit addresses PR review feedback (Phase 5 of `26_pr_05_address-review-comments.md`), the user's approval of the commit message also authorizes the push. Do not ask again. Push immediately, then proceed through Phase 6 (reply + resolve threads) and poll for new review feedback. The "Committed locally. Would you like me to push?" pattern does **not** apply to review-fix commits.

The two distinct flows:
- **Review-fix flow**: `commit (user approves message) → push → reply → resolve threads → poll` — automatic after commit message approval
- **Opening PR flow**: `commit → ask user → push → create PR` — user approval required before push

## Proactive Process Continuation
- After completing a task — including addressing review feedback and resolving threads — do not ask the user what to do next.
- Re-read `AGENTS.md` and the active workflow phase to determine the correct next step, then proceed with it.
- Only hand back to the user at a defined hard-stop point (user approval required, destructive action, ambiguous requirement) or at the end of a workflow phase.
- A question like "Would you like to merge?" is always wrong — the agent never merges and the next step is defined by the workflow, not by asking.

## Git Operations Flow
1. **Work through planned tasks** until reaching a logical completion point (e.g., entire subheading done).
2. **Announce completion**: "Finished [section name]. All [X] tasks complete. Should I commit this?"
3. **Wait for user confirmation** to proceed with commit.
4. **Stage the files**: `git add <files>` (only files related to this logical unit).
   - **Already-staged deletions**: Files removed earlier via `git rm` are already in the index.
     Do not include them in the `git add` command — they will cause `pathspec did not match` errors.
   - **Ignored files**: Files that were `git rm --cached` and then gitignored are already staged
     for deletion. Do not re-add them — git will refuse with an "ignored" error and abort the
     entire `git add` command (nothing gets staged).
   - **Before running `git add`**: Check `git status --short` first. Any file with a status letter
     in the **first column** (e.g., `D`, `M`, `A`) is already staged. Only `git add` files that
     have status letters in the **second column** (unstaged) or `??` (untracked).
   - **CRITICAL — only stage files that appear in `git status` output**: Never include a file in
     `git add` if it is absent from `git status --short`. Absent means one of:
       - gitignored (e.g. compiled artifacts, build output, `.gitignore`-listed paths)
       - a submodule path tracked by a nested git repo
       - a path that was already cleaned or deleted
     If git refuses to stage a file, **accept the refusal**. Investigate why the file is absent;
     do not use `git add -f` / `--force` to override the refusal. A git refusal is a signal, not
     an obstacle.
5. **Verify staged files**: Run `git status --short` and confirm all intended files are staged (status `A` or `M` in first column).
   - If files are missing or unexpected files are staged, fix staging before proceeding.
   - **CRITICAL**: Never run `git commit` without verifying staged files first.
6. **Commit**: Use structured commit template (see commit message rules).
   - **CRITICAL**: Always propose the commit message as rendered Markdown in chat first. Only construct shell commands after user approval.
   - **CRITICAL**: Always use temporary files for commit messages (see [Temporary Files](#temporary-files-for-commit-messages)).
   - Never use hardcoded paths like `/tmp/commit-msg.txt` - use `mktemp` or date-based unique names.
7. **Verify the commit succeeded**: Run this exact command after every `git commit`:
   ```bash
   echo "HEAD=$(git rev-parse --short HEAD)" && git --no-pager show HEAD --stat --format="commit %H%nDate: %ai%nSubject: %s" | cat
   ```
   - The `echo` prefix guarantees a plain ASCII first line, avoiding emoji rendering issues
     that can make `git log` output appear empty.
   - Check that:
     - The hash is **different** from the pre-commit HEAD (compare to what `git log` showed before staging).
     - The subject line matches the intended commit message.
     - The file list matches what was staged.
   - **If the hash is the same as before**: the commit silently failed. Check `git status` for
     remaining staged files and investigate.
   - **Do not run multiple exploratory git commands** trying to figure out what happened. One
     verification command is enough. If it's unclear, ask the user.
8. **Stop and inform**: "Committed locally. Ready to continue with next section, or would you like me to push/create a PR?"
9. **Only when explicitly instructed**:
   - Push: `git push` to current feature branch (avoid force pushes).
   - Create PR: Follow pull request rules.

## Git Safety
- **Force flags are categorically forbidden** — never use `-f` / `--force` on any git subcommand
  (`add`, `push`, `checkout`, `branch`, `reset`, `clean`, `merge`, etc.) unless the user
  explicitly requests it **in that conversation turn**. This includes:
  - `git add -f` — do not force-stage gitignored or refused files
  - `git push --force` / `--force-with-lease` — do not force-push without user approval
  - `git checkout -f` — do not force discard local changes
  - `git branch -D` — do not force-delete a branch (use `-d`; only escalate if user approves)
  - `git clean -f` — do not force-remove untracked files
  When in doubt, treat any `-f` flag as a destructive operation requiring confirmation.
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

1. **Check working tree first**:
   - If the working tree is not clean, stash **everything currently changed** before switching
     branches.
   - Use a named stash message so the purpose is obvious later.
   - Prefer one stash command for the current cleanup pass whenever practical.
2. **Checkout main branch**: `git checkout main`
3. **Fetch remote changes**: `git fetch origin`
4. **Update main**:
   - Prefer a clean fast-forward update when possible.
   - If the repository workflow or current state requires it, use a rebase-based pull for main.
   - **HARD STOP on conflicts**: if update conflicts occur, stop immediately and ask the user.
     Do not resolve them automatically.
5. **Delete local feature branch**:
   - Try `git branch -d <feature-branch>` first.
   - If it fails, assume a squash merge is likely before assuming something is wrong.
   - Verify the feature branch content is already contained in main by comparing the branch tips
     (for example `git diff --stat main..<feature-branch>` and confirm the diff is empty).
   - If the changes are present in main, use `git branch -D <feature-branch>` and continue.
   - If the changes are **not** present, stop and ask the user.
6. **Restore stashed local work**:
   - Only restore a stash if **this cleanup flow created one**. If you did not create a cleanup
     stash in the current post-merge pass, do not pop any stash.
   - Before restoring, verify the candidate stash is the one just created for this cleanup flow by
     checking its message, creation time, and expected file scope.
   - Do not pop an older unrelated stash just because one exists locally.
   - If you created a cleanup stash, restore it with `git stash pop`, not `git stash apply`.
   - If multiple named stashes were created as part of the same cleanup flow, pop only those
     explicitly intended stashes after the branch update and deletion succeed.
   - After each pop, verify the restored files still make sense on top of current `main` before
     continuing.
7. **Clean slate**: main is updated, the merged feature branch is removed locally, and any stashed
   local work is restored on top of current main.

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
