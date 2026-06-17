# Plan-Only Work Stays On Main

Plan drafting, roadmap updates, and implementation-plan writing do not require
a feature branch by default.

## Rules

- Stay on `main` for plan-only work unless the user explicitly wants a separate
  branch.
- Create a feature branch when implementation begins, or when the planning work
  is already bundled into an implementation slice.
- The feature branch must exist **before the first implementation edit**. Do
  not start coding, testing implementation changes, or preparing a PR from
  `main` and plan to branch later.
- If implementation accidentally starts on `main`, stop and create the feature
  branch immediately from the current dirty state before the next edit,
  QA pass, or persistence-prep step.
- If a branch was created too early for plan-only work, either move back to
  `main` or rename the branch to match the first actual implementation slice
  once that slice is chosen.

## Push Committed Main Work Before Stacking A Squash-Merged Branch

- If plan-only work is **committed** to local `main`, push `main` to its remote
  **before** creating or pushing a feature branch on top of it.
- Reason: those local `main` commits get folded into the feature branch's PR.
  When that PR is **squash-merged**, the remote `main` advances by one new commit
  whose parent is the old remote `main` — it does not contain the local commits as
  objects. Local `main` then **diverges** and can no longer fast-forward, so
  post-merge cleanup fails and a `git reset --hard origin/main` becomes necessary
  to reconcile.
- Two safe alternatives, either of which avoids the divergence:
  - push the committed plan work on `main` before stacking the branch, so remote
    `main` stays a true ancestor of the eventual merge; or
  - make the plan/docs commits **on the feature branch** rather than on local
    `main`, leaving local `main` at the pushed remote tip.
- This is workflow hygiene, not a recovery step. The reset is only the cleanup
  once the divergence has already happened.

## Intent

- Avoid branch overhead for docs and plans that are still shaping the work.
- Keep branch names tied to shippable implementation slices rather than vague
  planning phases.
- Keep all implementation, QA, and review-prep work anchored to a
  reviewable feature branch instead of retroactively moving dirty
  implementation off `main`.
