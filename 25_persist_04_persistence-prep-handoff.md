# Persistence-Prep Handoff

This file defines the hard handoff inside the **change-persistence phase**.

It starts after quality assurance has completed and before any persistence
action such as staging, committing, pushing, or review publication.

---

## Purpose

Quality assurance proves the change is ready for persistence consideration.
The persistence-prep handoff prepares the user to decide
whether to:

- proceed to commit, push, or review publication / PR creation,
- or ask for more cleanup first.

This keeps QA and persistence decisions distinct.

---

## Mandatory Sequence

Before any persistence action:

1. Confirm quality assurance completed and summarize the quality result.
2. Verify the checkout context:
   - the work is on the expected feature branch, or
   - the work is in the user-requested worktree for that feature.
3. Inspect the changed files and identify the persistence scope.
   - Enumerate every added, modified, and deleted path in the worktree, not
     only the paths you remember touching.
   - Include only files related to the current work.
   - Include docs and agent rules when they are part of the work.
   - Exclude unrelated local changes.
4. Run the provenance audit below and present its result.
5. Re-read the applicable persistence, commit-template, and commit-content rules.
6. Present the implemented improvement at a high level.
7. Present the proposed staging scope and commit strategy.
8. Present the proposed commit message(s) as rendered Markdown.
9. Pause for explicit persistence confirmation.

Do not commit, push, start review publication, or resolve review comments
before this handoff and approval.


---

## Provenance Audit — Files You Did Not Write

Every path that would be staged must be accounted for. Before staging, sort the
worktree's changes into two groups:

- **Accounted for** — you edited the file in this session, or a build or
  generator you ran produced it.
- **Unaccounted for** — everything else: a file you did not touch, one whose
  origin you cannot explain, or one you are unsure about.

The second group is a **hard handoff**. Present it as its own list, name each
path, say briefly what the file appears to be, and ask which of them should be
staged. Wait for the answer. Never stage an unaccounted-for file on the
assumption that it belongs to the change.

This audit is presented alongside the self-review findings and the diff-stat
summary, and carries the same weight as either.

### Deletions always need confirmation

Any deleted path is a destructive operation and needs explicit confirmation in
the turn it is staged, whether or not you made the deletion, and whether or not
an approved plan called for it.

### Bulk staging is what causes this

`git add -A`, `git add .`, and any other whole-worktree staging command sweep in
whatever happens to be lying in the tree — another session's scratch file, an
export, a credential dump, a colleague's work in progress. Prefer staging the
paths of the change explicitly. When a bulk command is genuinely the right tool,
the audit above runs before it, not after.

### Why

Reviewing your own work does not reveal a file you never touched: the
self-review is scoped to what you changed, so an unrelated file passes through it
untouched and unmentioned. The diff-stat summary does list it, but as one line in
a category that reads like noise, which is exactly the kind of line that gets
skimmed. Naming the unaccounted-for files as their own question is what forces
the check, and the person who knows where a stray file came from is the human,
not the agent.

Files land in a worktree for reasons the agent cannot see. The cost of asking is
one message. The cost of not asking is a commit that carries someone's private
data into shared history, where removing it needs a history rewrite and, once
pushed, help from the host that only the human can request.

---

## What To Present

The persistence-prep handoff should be concise and decision-oriented.

Include:

1. What the completed slice improved.
2. Any unaccounted-for or deleted files, with the staging question.
3. The suggested commit message(s).
4. Any persistence-phase caveat (for example, repositories where shared rule files have
   their own remote and must not be committed by the agent).
5. The explicit pause for confirmation.

The commit message proposal belongs here, not in the QA phase.

When proposing commit messages or PR body content:

- Show the rendered commit message in chat before any git command is run.
- Keep the `Quality assurance` or equivalent quality section outcome-focused.
- Do **not** include literal CLI commands in commit messages or PR bodies.
- Do **not** include file inventories there unless the user explicitly wants
  them.

---

## Relationship To Quality Assurance

- The repository's quality-gate rule defines the concrete gates.
- The change-review-and-size-assessment step contributes the QA review and size
  result.
- This file owns the hard handoff after quality assurance.

If quality assurance has not completed, do not use this file as if persistence
work may begin.
