# Commit Messages

Commit messages are a core part of the development workflow.
They are written **for humans**, not machines.
A good commit message provides clarity, intent, reasoning, and readability.

Use the structured template for all substantive feature work, fixes, and improvements.
Use the follow-up template for PR review fixes, bug fixes, and small corrections.
Keep documentation-only updates simple and focused.

---

## Before Writing: Understand the Full Scope

**Before drafting a commit message:**

1. **Understand what was accomplished.** What is the key achievement or change?
2. **When in doubt, ask the user** what the primary goal or message should be.
3. **Never just list what changed**—the diff already shows that.
4. **Focus on WHY and HOW**, not WHAT:
   - WHY: What problem did this solve? What gap did it fill?
   - HOW: What approach, decisions, or trade-offs were involved?
   - WHAT: Avoid restating file changes, line edits, or implementation details visible in the diff.

The commit message should tell the story behind the change, not narrate the change itself.

---

## Drafting Workflow

**Always propose the commit message as rendered Markdown in the chat first.**

- Draft the message and show it to the user for review.
- Only construct shell commands (`mktemp`, `cat`, `git commit -F`) after the user approves the content.
- This avoids constant rejection of shell commands and makes the message easy to read and discuss.

---

## Critical Rules

1. **Always start with a fitting emoji.**
   It visually conveys the type of change and increases scan-ability.

2. **Write for human readers**—reviewers today and future contributors tomorrow.
   The message must be easy to understand without reading the diff or knowing the conversation.

3. **Explain the goal, the why, and the reasoning.**
   The diff shows the *what*. The commit message explains the *motivation* and *decisions*.

4. **Stay structured and concise.**
   Follow the template exactly. No extra sections; no noise or verbose play-by-play.

5. **Lead with user value.**
   When technical changes accompany a feature, keep the user-facing purpose at the top.

6. **Use short paragraphs and readable sentences.**
   The message must be digestible in ~30 seconds.

7. **Never include:**
   - debug notes
   - exploration steps
   - implementation details evident from the diff
   - internal monologue
   - overly technical or low-signal text

---

## Length Target

**The entire message (title through last section) should be under 30 lines.**
If it exceeds that, you are probably narrating the diff instead of summarizing decisions.
Less is more — as long as all key ideas are captured, shorter is better.

---

## Narrative Requirements (Critical)

A high-quality commit message body **must**:

- Describe the **goal** of the change. What is the purpose?
- Explain **why** the change was needed. What problem or gap existed?
- Summarize the key **trade-offs**, considerations, and decisions involved.
- Provide context so a reviewer or future contributor understands intent quickly.
- Be **reader-friendly**: short paragraphs, clear reasoning, no jargon unless necessary.
- Stand on its own—someone reading the commit without the PR or conversation should understand the intent.

Low-quality, non-narrative commit messages must never be produced.

---

## Template

```markdown
<emoji> <short description>

## Summary
<One sentence: human-level outcome (what changed for a user/system)>

## Context / intent
<1–3 sentences: why this was needed / why now; key constraint; key trade-off if relevant>

## Key changes
- <1–5 bullets max: high-impact deliverables grouped by theme or user feature>
- <Less is more — capture key ideas, skip everything else>

## Additional changes (optional)
<Out-of-scope changes that are not a subset of key changes but add value>
- <1–3 bullets max>

## Validation / quality
- **Tests added/updated:** <type + count>
- **Tests run:** <what you executed, result>

## Future improvements (optional)
<Only if the user explicitly called out a deferral or accepted limitation>
- <1–2 bullets max; omit section if none>

## References (optional)
<Only if the user explicitly linked an issue or external doc; omit if none>
```

---

## When to Use Which Template

The **full template** (above) is for **primary feature commits and PR bodies** — work that
introduces new functionality, establishes new patterns, or makes substantial architectural
changes. These are the commits that become the permanent record on `main` (especially with
squash merge).

The **follow-up template** (below) is for everything else:
- Addressing PR review comments
- Bug fixes
- Small misses or corrections
- Refactoring follow-ups
- Any commit that is a response to feedback rather than original work

When a PR has a single commit, `gh pr create --fill` uses the full template as the PR body.
Follow-up commits within the same PR use the follow-up template.

---

## Follow-Up / Fix Commit Template

```markdown
<emoji> <short description>

## Summary
<One sentence: what was addressed>

## Key changes
- <What was fixed and how — focused on the actual issues addressed>
- <Each bullet = one fix or closely related group of fixes>
- <Small incidental changes (config cleanup, dev artifact removal) do not need listing>

## Validation / quality
- **Regressions:** <Were any regressions introduced? Did the fix improve the quality situation?>
- **Tests:** <Count of tests added/updated for these fixes, total suite result>
```

### Follow-Up Template Rules

1. **No Context / Intent section.** The parent commit or PR body provides the context.
   The follow-up exists to address feedback — the "why" is self-evident.

2. **No Additional Changes section.** If small incidental things come along (a gitignore
   tweak, removing a stale file, config cleanup), they just go with it. They don't need
   to be accounted for in the message. Only list them if a reviewer would genuinely ask
   "why is this here?"

3. **Key changes = what was addressed.** Each bullet describes a fix or improvement that
   directly responds to feedback. Meta-artifacts (ADRs, agent rules) created as part of the
   fix process are not key changes — they support the fix but are not the fix itself.

4. **Validation focuses on quality delta.** Did we add regression tests? Did the fix
   improve coverage or correctness? Keep it to 1–2 lines.

5. **Length target: under 20 lines.** Follow-ups should be quick reads.

---

## Section Guidance

### Summary — The Product Manager Test

State the outcome in a way a **product manager** would understand.
If your summary requires knowledge of specific classes, frameworks, or file paths, rewrite it.

Ask yourself: "Would a non-engineer understand what this delivers and why it matters?"
If no, rewrite it.

### Key Changes — Features and User Outcomes, Not Code

Key changes describe **what users or the product can now do** — not what files changed or
how the code works. Write as if explaining to a product owner, not a code reviewer.

**Default: product language.** Technical language is only acceptable when:
- The change is infrastructure with no user-facing equivalent (e.g., "Rate limiting on sign-in to prevent abuse")
- The change is security-critical and the nature of the protection is the point (e.g., "Session fixation prevention on sign-in")
- The audience is explicitly engineers (e.g., a pure refactor or migration)

**Never acceptable in key changes:**
- API route paths (`/api/auth/start`)
- Class or method names (`AuthController`, `findByEmail()`)
- File names or packages
- HTTP status codes or headers
- Database column or table names
- Framework or library internals

**Group by user feature or outcome**, not by layer (backend/frontend/test).

**Good:** "Users can sign in with just their email — no password needed"
**Good:** "Rate limiting on sign-in to prevent abuse"
**Acceptable:** "Session fixation prevention on authentication"
**Bad:** "Added POST /api/auth/start endpoint with Bucket4j rate limiting"
**Bad:** "AuthController now uses tryConsumeAndReturnRemaining for Retry-After header"
**Bad:** "Frontend httpClient now uses credentials: include"

If a bullet would make sense only to someone who has read the diff, it is too technical.
Rewrite it as the *outcome* that diff enables.

**Group** related work into one bullet per theme. One bullet per file or class is always wrong.

### Additional Changes — Out-of-Band Work Only

This section is for changes that are **disjoint from the key changes** — not a subset of
them. Think: an unrelated refactoring that added value, or a fix for something outside
the feature scope that was the right time to address.

If something was part of building the feature (bug fixes during development, test
builder cleanup, seed data corrections), it belongs in the diff, not in the commit
message. The reviewer wouldn't ask "why is this here?" so don't call it out.

### Validation / Quality — Keep It Minimal

State what was tested and the result. Two lines. No commands, no file lists.

---

## Self-Review Checklist

Before finalizing a commit message, verify:

- [ ] Can I explain this commit in one sentence to a product person?
- [ ] Does every Key Changes bullet describe a *user outcome or product capability*, not a file/class/method/route?
- [ ] Does any bullet contain a route path, class name, method name, or HTTP header? If yes, rewrite or remove it.
- [ ] Would removing any bullet lose information a reviewer needs? If not, remove it.
- [ ] Is the total message under 30 lines?