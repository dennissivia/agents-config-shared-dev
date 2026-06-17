# Worktree Checkout Mode

Worktrees are supported for parallel agent work, but they are not the default.
The default checkout mode remains the current working tree plus a regular
feature branch.

## When To Use A Worktree

- Use a worktree only when the user explicitly requests worktree-based work or
  parallel agent work that should use isolated checkouts.
- Do not switch to worktrees only because a worktree container exists.
- Worktree mode changes only the checkout location. The planning,
  implementation, quality assurance, persistence-prep, review, and
  integration/deployment workflow stays the same.

## Worktree Container Selection

When worktree mode is requested, check the repository root for an existing
container in this order:

1. `.wt/`
2. `.worktrees/`
3. `worktrees/`

Use the first existing container in that order.

If none of those containers exists, pause and ask the user where the worktree
should be created before creating a new container or falling back to a regular
checkout.

## Branch And Directory Rules

- Use the same branch naming rules as the standard workflow:
  `feature/<description>`, `bugfix/<description>`, `tech/<description>`, or
  `docs/<description>`.
- Do not invent worktree-specific branch prefixes.
- Derive the worktree directory name from the branch description using safe path
  characters: lowercase letters, numbers, and hyphens.
- Before creating the worktree, state the selected container, target directory,
  base branch, and feature branch.
- Creating a worktree with a new branch is a branch-creation action; follow the
  same confirmation and safety rules that apply to a regular feature branch.

### Seed From Local `main`, Never From `origin/main`

- Seed a new worktree branch from the **local** integration branch (`main`), not
  from the remote-tracking ref (`origin/main`):

  ```
  git worktree add -b <branch> <path> main
  ```

  Basing the new branch on the local `main` ref creates it from `main` without
  checking `main` out in the worktree, which git allows.
- Do **not** seed from `origin/main` (for example
  `git worktree add -b <branch> <path> origin/main`). Creating a branch from a
  remote-tracking ref makes git auto-configure `origin/main` as the new branch's
  upstream (`branch.autoSetupMerge` defaults to `true`). With
  `push.default=upstream`, a destination-less `git push` then resolves to
  `refs/heads/main` and lands the commit on `main`. A plain `git rebase` does not
  cause this; only the seed point does.
- Right after creating the worktree branch, **verify `main` is not its upstream**:

  ```
  git -C <path> branch -vv
  ```

  The new branch line must not show `[origin/main]` (or `[main]`) as its upstream.
  If it does, repoint it before any push — clear it with
  `git -C <path> branch --unset-upstream`, and only set an upstream once the
  branch exists on the remote under its own name.
- Until the branch's upstream is known to point at its own remote branch, push
  with an explicit `src:dst` refspec, never a destination-less push:

  ```
  git push origin <branch>:refs/heads/<branch>
  ```

## Rule Loading In Worktrees

Worktree checkouts may not contain the full rule source. In this repository,
compiled rule references and shared rule folders can be ignored files, so a new
worktree may contain only `.agents/local/` and miss `.agents/shared/` or
`AGENTS.md`.

When operating inside a worktree:

- Do not use the worktree's `.agents/` directory as the rule baseline unless it
  has first been verified to contain the full shared and local rule set.
- Prefer the primary checkout's compiled `AGENTS.md` or the primary checkout's
  complete `.agents/` source as the rule baseline.
- Before implementation work begins, copy the primary checkout's current
  compiled `AGENTS.md` into the worktree root so the worktree has a complete
  local context anchor.
- If the rules changed, regenerate `AGENTS.md` from the primary checkout using
  the repository compile script, then copy the regenerated file into the
  worktree.
- Treat the copied worktree `AGENTS.md` as generated context. Do not treat it as
  the source of truth for rule changes.

## Parallel Work Safety

- Each parallel feature or fix gets its own branch and its own worktree.
- Do not use a shared worktree for multiple active features.
- Do not use worktrees to bypass branch-size checks, QA gates,
  persistence-prep handoffs, commit approval, push approval, review approval,
  or integration/deployment approvals.
