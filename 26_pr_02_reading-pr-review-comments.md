# Reading PR Review Comments via GitHub CLI

When reviewing automated code review feedback (Copilot, etc.) on a pull request,
use the GitHub CLI (`gh`) to fetch both the review summary and inline comments.

---

## Review Summary (top-level review body)

```bash
gh api repos/{owner}/{repo}/pulls/{pr_number}/reviews \
  --jq '.[].body' | cat
```

To target a specific reviewer (e.g., Copilot):

```bash
gh api repos/{owner}/{repo}/pulls/{pr_number}/reviews \
  --jq '.[] | select(.user.login == "copilot-pull-request-reviewer") | .body' | cat
```

## Inline Review Comments (code-level feedback)

Inline comments are **separate from the review body** and live under a different endpoint.

```bash
gh api repos/{owner}/{repo}/pulls/{pr_number}/comments \
  --jq '.[] | "---\nFile: \(.path):\(.line)\n\(.body)\n"' | cat
```

---

## Critical: Pager Safety

**Always pipe through `| cat`** to avoid interactive pager sessions (`less`, `more`)
that block terminal output and require manual exit (`q` or Ctrl+C).

Alternatively, redirect to a temp file and read it:

```bash
gh api repos/{owner}/{repo}/pulls/{pr_number}/comments \
  --jq '.[] | "---\nFile: \(.path):\(.line)\n\(.body)\n"' \
  > /tmp/pr-comments.txt && cat /tmp/pr-comments.txt
```

---

## Common Mistakes

- **Using `gh pr view --json comments`**: This returns PR-level conversation comments,
  **not** inline code review comments. They are different API resources.
- **Using `gh pr view --json reviews`**: Returns review summaries but truncates the body
  in the pager. The API endpoint gives the full text.
- **Forgetting `| cat`**: Causes the terminal to hang in an interactive pager session.

---

## Quick Reference

| What | Endpoint | jq filter |
|------|----------|-----------|
| Review summary | `pulls/{n}/reviews` | `.[].body` |
| Inline comments | `pulls/{n}/comments` | `.[] \| {path, line, body}` |
| Comment count | `pulls/{n}/comments` | `length` |
