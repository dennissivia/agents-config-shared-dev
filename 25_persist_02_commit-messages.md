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

### Always Offer Five Alternative Emojis At The Approval Step

Whenever a commit message (or PR title) is presented for approval, also offer
**five alternative leading emojis** below the message. This lets the user accept
the wording as-is while swapping the emoji in one step.

- List exactly five alternatives, each a fitting emoji for the actual change
  with a few words on why it fits.
- All five must satisfy the emoji-fit and positivity rules in this file and in
  `25_persist_06_commit-and-pr-title-prefixes.md`. Never propose a risky,
  negative, or potentially offensive emoji.
- The emoji already used in the drafted subject is the recommendation; the five
  alternatives are in addition to it, not a replacement for choosing well first.
- If the user picks an alternative, reuse the approved wording verbatim and only
  change the leading emoji.

---

## Writing Stance and Language (Mandatory)

Every commit message and PR body **must** be written from the perspective of a
**technical project manager or senior engineering manager addressing product
and business stakeholders**, not from the implementing engineer's
perspective. This is a hard requirement and applies to every commit and PR
in every repository.

ADRs, agent plans, implementation plans, and other internal engineering
artifacts may use more technical language, but commits and PRs always use
this business-facing stance.

### The audience never changes; the vocabulary does

The reader is a stakeholder: someone who owns, reviews, or inherits the work — a
product owner, an engineering manager, a maintainer returning to it in a year.
They are not the person who wrote the diff, and they do not have it open.

What changes is the kind of work, and each kind has vocabulary that fits it.

| Kind of change | Summary and context | Key changes |
|---|---|---|
| A user-facing feature | Product language: what someone can now do, and why it matters to them. | The capabilities a person gains. |
| Technical work serving a feature | Product language for the capability it serves, technical language for the work itself. | What the system can now do, tied to the feature it serves. |
| A technical outcome in its own right — security, rate limiting, logging, quality of service, data migration | The technical concept, named accurately. | What the system now does, and the risk removed or the guarantee established. |

Technical work usually has no user-facing outcome, and inventing one produces
worse writing than describing the change accurately. Where such a change does
reach a user, it is normally implicit or visible only when something goes wrong.
Say that plainly rather than dressing it as a feature.

### Precision is required in every kind

No vague or generic phrasing, whatever the vocabulary. This carries the same
weight as the forbidden language below.

- Use the system's own nouns for whatever changed: global rate limit counter,
  per-IP bucket, proof of work, session cookie, database migration, origin
  check. A name the project already uses is the right word for it.
- Never replace such a noun with a metaphor or a paraphrase. "The allowance
  everyone shares" is not a plainer way of writing global rate limit; it is a
  vaguer one, and it leaves the reader guessing which real thing is meant.
- Never refer to something the reader has not been given. A bare "the date",
  "the value", "the field", "the flag" assumes a referent that exists only in
  the diff, and the reader has not seen the diff. Name it in full wherever it
  could be ambiguous — "the schedule's start date", not "the date" — including
  the second and third time it appears. Shortening a reference to something
  already named in the same paragraph is fine; shortening it to something named
  nowhere is not.
- Never settle for a generic verb where a specific one exists. "Improve",
  "update", "handle", "address", "streamline", "optimise", "enable", "provide",
  "ensure", "support", "make" and "give" describe nothing on their own. Name the
  concrete action: add, remove, rename, store, return, show, wait, reject.

### Where the line falls: detail, not vocabulary

Technical vocabulary is not the problem; excessive detail is. Terms such as rate
limiting, quota, proof of work, migration, and cache are understood by any
stakeholder and belong in the message.

These stay out whatever the audience, because they describe how rather than
what:

- class, method, file, and package names
- route paths, HTTP headers, numeric status codes, database columns
- implementation mechanics and named internal patterns
- library and framework internals

When the change is about how the system responds, name the response by its kind in
words: "return a not found error", "return a conflict error", "show an error
message". That is the plain description of the behaviour; replacing it with a verb
that hides it ("report", "answer", "refuse") is the failure, not naming it.

The test is not "would a non-engineer recognise this word", which rejects
perfectly ordinary nouns like counter and quota. It is:

> Does this word name the actual thing, and could the reader find that thing in
> the documentation or in the running system?

### Forbidden language

The following must not appear in commit messages or PR bodies:

- **AI or agent vernacular**: "lifts X out of", "wires up", "stitches in",
  "spins up", "lands", "ships", "in flight", "stable anchor", "scaffolding
  for", "happy path baked in".
- **Developer or internet slang**: "DX", "QoL", "shiny", "battle-tested",
  "first-class", "low-hanging fruit", "drop in", "swap out", "plumbing".
- **Internal labels without explanation**: "I9", "M3", "Sprint 4", version
  codenames, bare ticket IDs. Replace each with the product or engineering
  outcome behind the label.
- **Process meta-commentary about the commit boundary**: "this commit", "as
  its own change", "in a later PR", "in the next commit". Speak about the
  product and the codebase, not about how the work was sliced.
- **Colloquial verbs that hide the change**: "consistency pass", "cleanup
  sweep", "polish", "tidy".
- **Human acts attributed to the system or the change**: "answer a request",
  "report an edit as not saved", "ask the member to …", "tell the user", "refuse",
  "a refusal". A system does not answer, report, ask or refuse; it returns an
  error response, stores or rejects a value, or shows a message. Write those.
  Bad: "Report an edit as not saved and ask the member to check it." Good:
  "Return a conflict error when an edit's write changes nothing, and show a
  message to check the checklist and make the change again."
- **Permissive "let" framing**: "Let a user pick a date", "Let the importer
  skip duplicates", "Allow X to happen". It reads as though the change grants
  permission for something to occur by itself rather than stating what was
  built. Name the capability instead: "Add a start date to the schedule form",
  "Skip duplicates on import".
- **Invented collective actors**: "a household can", "the team sees", "the
  family gets". Groups do not click, choose, or read; a person does. Write
  "users", "a user", or drop the actor entirely — "Selecting a start date now
  anchors the schedule" — and keep real system actors such as the scheduler,
  the importer, or the browser, which do act.

### Fallback

If a change cannot be described without forbidden language, the description
is not ready. Re-read the diff and the goal, then rewrite at the
product-and-business level. Do not commit until the description holds at
that level.

---

## Critical Rules

1. **Always start with a fitting emoji.**
   It visually conveys the type of change and increases scan-ability.
   Choose the emoji by semantic fit. Do not default to the same emoji when a
   more accurate one exists for the actual change.

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
   - unexplained internal increment IDs, roadmap IDs, ticket labels, or version shorthand unless the user explicitly wants them in the message
   - colloquial labels such as "consistency pass", "cleanup sweep", or similar shorthand that hides the actual reason for the change
   - contrastive or negative framing such as "this is X, not Y" or lists of things the change does not do
   - self-justifying language that defends scope instead of explaining the context that led to the work

---

## Subject Prefix Format

Commit subjects use a capitalized, long-form intent prefix followed by a colon.
The prefix makes the change type scannable without adopting Conventional Commits.

Required subject shape:

```markdown
<emoji> <Prefix>: <imperative subject>
```

Use these prefixes:

- `Feature:` for user-facing functionality and behavior changes
- `Bugfix:` for bug fixes and production regressions
- `Tech:` for internal engineering work, refactors, infrastructure, or quality work
- `Docs:` for documentation, ADR, roadmap, README, or rule-only changes

Examples:

```markdown
🔐 Feature: Add passkey sign-in to auth page
🐛 Bugfix: Fix mobile overview layout regressions
🛠️ Tech: Clean up test data builders
📝 Docs: Document repository title prefix convention
```

Rules:

- Use the colon form in commit titles, never slash prefixes such as `feature/` or `bugfix/`.
- Use long-form prefixes; do not use abbreviations such as `Feat:`, `Fix:`, or `fix:`.
- Keep the subject after the prefix imperative / present tense.
- Branch names are different: they use lowercase slash prefixes such as `feature/...`
  and `bugfix/...`.

---

## Length Target

**The entire message (title through last section) should be under 30 lines.**
If it exceeds that, you are probably narrating the diff instead of summarizing decisions.
Less is more — as long as all key ideas are captured, shorter is better.

---

## Line Width

Wrap body lines at **72 characters**. The subject line stays well under that.

Narrow lines stay readable in primitive tools such as a terminal `git log`, match
what mailing lists expect, and are what git tooling is built around. Modern tools
are more forgiving, but the convention costs nothing and keeps the message legible
everywhere.

Do not wrap a URL or a long path that would break when split.

---

## Narrative Requirements (Critical)

A high-quality commit message body **must**:

- Describe the **goal** of the change. What is the purpose?
- Explain **why** the change was needed. What problem or gap existed?
- Summarize the key **trade-offs**, considerations, and decisions involved.
- Provide context so a reviewer or future contributor understands intent quickly.
- Be **reader-friendly**: short paragraphs, clear reasoning, no jargon unless necessary.
- Stand on its own—someone reading the commit without the PR or conversation should understand the intent.

The `Context / intent` section must explain the prior condition, inconsistency, or pressure that led to the change. It must not read like a defense of scope, a rejection list, or a note about what the implementation deliberately avoided.

Low-quality, non-narrative commit messages must never be produced.

---

## Imperative Mood

Use **imperative / present-tense** commit subjects.

Good:

- `add passkey sign-in to auth page`
- `fix passkey cancellation handling`
- `document repository commit prefixes`

Bad:

- `added passkey sign-in to auth page`
- `fixed passkey cancellation handling`
- `documented repository commit prefixes`

The subject should read like an instruction to the codebase:

- `add`, not `added`
- `fix`, not `fixed`
- `document`, not `documented`

The body uses the same mood. A commit message is attached to a change, not to the
code the change leaves behind, so write it as if the change is about to be applied
rather than as a report of what was just done.

Good:

- `Add a public API that accepts contact requests.`
- `Restrict who can read stored contact details.`

Bad:

- `Added a public API that accepts contact requests.`
- `This change restricts who can read stored contact details.`

### Active Voice

Write in the active voice, with the change itself as the actor. Do not describe the
system as if it changed on its own, and do not hide the actor behind a passive
construction. This holds unless the user explicitly asks for different phrasing.

Good:

- `Implement Y to make X be Z.`
- `Add capability A to system B.`
- `Implement new rules for X so it does Y.`

Bad:

- `X becomes Z.`
- `Y gains Z.`
- `Support for Z was added to Y.`
- `The page now carries a contact form.`

The test: name the verb the change performs and the thing it acts on. If the
sentence has no actor, or the system is the subject of a vague state change
("becomes", "gains", "carries"), rewrite it.

The change acts on the codebase: it adds, removes, renames, returns, stores. It does
not act like a person. When a sentence is about what the running system does after
the change, give the system a concrete verb in a clause that follows the change's
verb: "Return a not found error when the checklist no longer exists", not "Answer a
missing checklist with not found". Never pick a verb only so that a sentence can
open with an outcome.

This guidance is compatible with many common Git recommendations and should be
followed unless a project defines a stricter local convention.

---

## Emoji Fit

The emoji should match the change category rather than being chosen by habit.

Examples:

- `🔐` auth, security, passkeys
- `✨` new feature or new capability
- `🛠️` technical/internal improvement
- `📝` docs, ADR, README, rules
- `🐛` bug fix
- `♻️` refactor
- `✅` test-focused or validation-focused work

The goal is not novelty. The goal is to avoid repetitive, low-signal emoji use
when a more accurate symbol exists.

Any emoji must carry a positive, lighthearted, or positively-engaging
association — or at most neutral. Never use an emoji that is risky, negative, or
that could offend, insult, or demean any person or group. When the most "accurate"
symbol carries such a risk, choose a positive or neutral alternative instead
(for example a colourful or upbeat symbol).

---

## Template

```markdown
<emoji> <Prefix>: <short imperative description>

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
- **Test focus:** <High-level use cases or behavior areas the tests focused on; no file names or test counts>
- **Automated tests:** <Pass/fail outcome and new/updated unit, integration, or E2E coverage areas>
- **Manual tests** (optional): <Manual QA explicitly performed or requested by the human>
- **Findings** (optional): <Bugs found during QA that were fixed immediately or added to the backlog>

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
<emoji> <Prefix>: <short imperative description>

## Summary
<One sentence: what was addressed>

## Key changes
- <What was fixed and how — focused on the actual issues addressed>
- <Each bullet = one fix or closely related group of fixes>
- <Small incidental changes (config cleanup, dev artifact removal) do not need listing>

## Validation / quality
- **Test focus:** <High-level use cases or behavior areas the tests focused on; no file names or test counts>
- **Automated tests:** <Pass/fail outcome and new/updated unit, integration, or E2E coverage areas>
- **Manual tests** (optional): <Manual QA explicitly performed or requested by the human>
- **Findings** (optional): <Bugs found during QA that were fixed immediately or added to the backlog>
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

4. **Validation focuses on quality delta.** State the use cases covered and
   whether automated tests passed. Mention manual QA or findings only when
   they add real information.

5. **Length target: under 20 lines.** Follow-ups should be quick reads.

---

## Outcome-First Phrasing (Every Section)

Every sentence and bullet leads with the **outcome** — the capability gained, the risk
removed, the guarantee established. The mechanism that delivers it follows, and is
named whenever naming it tells the reader something they need.

This holds for technical changes as much as for features. Leading with the mechanism
and trailing the purpose is the common way to get it backwards.

The shape is: `<outcome>`, optionally followed by `by <means>` / `via <means>`.

Outcome-first is about order, not about inventing a verb. When no plain verb puts the
outcome first, state what the change does in plain words and follow it with why it
matters: "Return a conflict error when an edit's write changes nothing, so a lost
edit is not shown as saved." A sentence that opens with a made-up action ("Report an
edit as not saved", "Open checklists to collaborators") fails both rules.

This applies to commit messages and pull-request bodies alike, and to every section of
both.

### Summary

State what the change makes possible, not what was built.

**Good:**
> Add a public API that serves the product catalogue.

**Good — outcome plus the means that delivers it:**
> Add a public API that accepts contact requests, for the contact form on the
> marketing page.

**Good — a technical change, outcome first and the technique named:**
> Reduce the risk of automated abuse by adding bucket-based rate limiting to the
> public form routes.

**Bad — mechanism first, purpose trailing:**
> Implement rate limiting in order to avoid abuse.

**Bad — names the artifact and stops:**
> New endpoint for contact requests.

### Context / intent

Lead with the capability the work serves, then what the change adds to serve it.

**Good — user-facing work:**
> Add a contact form to the marketing page, so visitors can reach us without an
> account.

**Good — technical enabler:**
> Add a public API that accepts contact requests, for the marketing page's contact
> form. The same API serves any later flow that captures an enquiry from an
> unauthenticated visitor.

A technical enabler still has an outcome. When the capability it unlocks does not ship
in the same change, name that capability and say plainly that it is not in use yet.
Say whether the new surface is public or authenticated when that distinction carries
product or security meaning.

### Key changes and every other change detail

Each bullet opens with the outcome, in the same shape. This holds for key changes,
additional changes, and any other line that describes part of the change.

**Good:**
- Add a contact form to the marketing page.
- Secure the public submission endpoint against automated abuse by limiting how often
  it can be called.
- Protect stored contact details by restricting who can read them.

**Bad — leads with the mechanism:**
- Added a rate limiter to the contact endpoint.
- New request-size filter.
- Refactored the contact service.

The outcome comes first. A bullet written in the past tense ("Added", "Refactored"), or
whose verb acts on an internal mechanism instead of on what the reader can see or rely
on, is written the wrong way round. Rewrite it with a plain imperative verb, and name
the means afterwards only if it matters. Do not invent a verb to achieve the order; see
the rule above.

### When the deliverable is a definition

Rule sets, workflows, specifications, ADRs, and documentation deliver a
definition. The definition is the outcome. There is no separate capability
behind it, because nothing changes until someone applies it.

Say what the change defines and how far the definition reaches.

**Good:**
- Define the lifecycle: ideation, research, writing, verification, release,
  distribution, assessment, and maintenance.
- Define four human review gates and what each one approves.
- Define the artifact layout that separates research from the published text.

**Bad — states an effect the change does not yet have:**
- Hold publication behind four explicit approvals.
- Keep claims traceable by requiring a durable source record.

The bad form claims a behavior change that has not happened. Name the scope of
what is defined and let the reader infer the effect.

---

## Section Guidance

### Summary — The Stakeholder Test

State the outcome in the vocabulary that fits the kind of change, and state it
precisely. If the summary needs a class, a framework, or a file path to make sense,
rewrite it.

Ask: "Would someone who owns this work, but has not read the diff, know what changed
and why it matters?" If no, rewrite it. Do not ask whether every word would be
familiar to a non-engineer — that question drives out accurate nouns like counter,
quota, and migration, and what replaces them is always vaguer.

#### State the outcome, not the mechanism that produces it

The summary names what is true for the product now. It does not describe how the
change works, which artifact replaced which, or where something is served from.

**Good:**
> Showcase the application's features to visitors with a full marketing page.

**Bad — describes a mechanism:**
> The new page becomes what visitors see at example.com.

**Bad — names a moving part:**
> The build output directory is now generated from the compiled application.

Domain names, directory names, and file formats are mechanism. If removing them
from the sentence destroys its meaning, the sentence was about the mechanism.

This is not a licence to remove technical nouns. The name of a control, a limit, or
a stored thing is what the sentence is about, not how it is built.

#### Say what is newly true, never restate the previous change

Work often lands across several changes in one release. Each summary must claim
only what **that** change makes true. Restating the outcome an earlier change
already claimed makes the message read as empty, because the reader has seen the
claim before and learns nothing.

Before writing the summary of a change that continues earlier work, read the
previous change's summary and ask: "what can the product do now that it could not
before this one?" That answer is the summary.

**Example of the failure.** A first change builds a marketing page; its summary
is "Showcase the application's features with a full marketing page." A second change
publishes that page and adds its first automated tests. Repeating the first summary
claims an outcome already delivered.

**Good, for the second change:**
> Publish the new marketing page and protect it with the site's first automated
> tests.

A useful check: if the summary would have been equally true one change ago, it is
not the summary of this change.

### Context / intent — Explain Why, Not What Was Excluded

The `Context / intent` section explains why this work happened now and what condition made it necessary.

Good context usually includes one or more of:

- the inconsistency, bug, risk, or workflow problem that existed before the change
- the product or engineering pressure that made the change worth doing now
- the decision path or constraint that shaped the chosen implementation

Do not write this section as a contrast against alternatives or exclusions.

**Good:**
> Before this change, similar forms validated input in different ways, which made trimming, whitespace rejection, and optional-field normalization inconsistent across the UI.

**Good:**
> We already had shared validation helpers, but several submit paths still used ad hoc guards. Aligning those paths now reduces user-facing inconsistency and makes later validation work easier to evolve.

**Bad:**
> This is a consistency pass, not a redesign.

**Bad:**
> This change does X and does not do Y.

**Bad:**
> We intentionally did not adopt a bigger architectural change here.

### Key Changes — What The Work Delivers, Not How It Is Built

Key changes describe what the product or the system can now do. They never describe
which files changed or how the code is arranged. The vocabulary follows the kind of
change, as set out under Writing Stance.

**For a user-facing feature**, each bullet names a capability a person gains.

> Add sign-in with an email address alone, with no password to remember.

**For technical work that serves a feature**, each bullet names what the system can
now do, and ties it to the capability it serves.

> Serve the household roster from one query so the members screen loads in a single
> request.

**For a technical outcome in its own right**, each bullet names the concept
accurately and states the risk removed or the guarantee established. Do not invent
a user-facing outcome that the change does not deliver.

> Reduce the risk of automated abuse by adding bucket-based rate limiting to the
> public form routes.

> Prevent session fixation on sign-in, so a session identifier issued before
> authentication cannot be reused after it.

> Increase the global rate limit for contact messages to a level suitable for
> production traffic.

**Never acceptable in key changes**, whatever the kind:

- class, method, file, or package names
- route paths, HTTP headers, numeric status codes, database columns
- library and framework internals
- vague or generic phrasing in place of the name of the thing that changed

**Bad — names the code rather than the work:**
> Added POST /api/auth/start endpoint with Bucket4j rate limiting.

**Bad — describes the mechanics:**
> AuthController now uses tryConsumeAndReturnRemaining for the Retry-After header.

**Bad — replaces the noun with a metaphor:**
> Keep the allowance every visitor shares out of reach of a request that paid
> nothing.

That last one is the failure this section exists to prevent. It is not plainer than
naming the global rate limit counter; it is vaguer, and the reader cannot tell
whether the writer knew which control was meant.

**Group by capability or outcome**, not by layer. One bullet per file or class is
always wrong.

If a bullet would only make sense to someone who has read the diff, it is describing
the code. Rewrite it as what the diff achieves.

### Additional Changes — Out-of-Band Work Only

This section is for changes that are **disjoint from the key changes** — not a subset of
them. Think: an unrelated refactoring that added value, or a fix for something outside
the feature scope that was the right time to address.

If something was part of building the feature (bug fixes during development, test
builder cleanup, seed data corrections), it belongs in the diff, not in the commit
message. The reviewer wouldn't ask "why is this here?" so don't call it out.

### Validation / Quality — Outcome-Level Reporting

State what behavior was covered and whether the relevant automated checks passed.
Keep the section readable for product and engineering stakeholders.

Required fields:

- **Test focus:** one high-level line naming the use cases or behavior areas
  that received test attention. Do not list file names, test method names, or
  test counts.
- **Automated tests:** one line with the pass/fail outcome plus the kinds of
  automated coverage added or updated, such as unit, integration, or E2E tests
  for a named behavior area.

Optional fields:

- **Manual tests:** include only manual QA that the human explicitly requested
  or performed. Keep it brief and outcome-focused.
- **Findings:** include only bugs or quality issues found during QA that were
  fixed immediately or recorded for follow-up.

Omit optional fields when they do not add useful information. No commands, file
inventories, tool flags, or raw test counts. The reader cares about the
guarantee, not the keystrokes.

---

## Self-Review Checklist

Before finalizing a commit message, verify:

- [ ] Have I picked the vocabulary from the kind of change — product language for a user-facing feature, technical language for a technical outcome — rather than defaulting to one of them?
- [ ] Would someone who owns this work, but has not read the diff, know what changed and why it matters?
- [ ] Does the summary state an outcome rather than a mechanism, and would it still mean something with every domain, directory, and file name removed?
- [ ] Would the summary have been equally true before this change? If yes, it restates earlier work and is not this change's summary.
- [ ] Is the language free of AI vernacular ("lifts", "wires up", "stable anchor"), developer slang ("DX", "first-class", "plumbing"), internal labels ("I9", "M3"), or process meta-commentary ("this commit", "in the next PR")?
- [ ] Does every Key Changes bullet describe what the work delivers, not which files changed or how the code is arranged?
- [ ] Does every noun name the actual thing — the counter, the limit, the proof, the migration — with no metaphor or paraphrase standing in for it?
- [ ] Is every verb specific? "Improve", "update", "handle", "address", "streamline", and "optimise" describe nothing on their own.
- [ ] For a technical change, have I resisted inventing a user-facing outcome it does not deliver?
- [ ] Does every bullet open with the outcome, with the mechanism following it — never past tense ("Added…", "Refactored…"), a bare component name first, or the purpose trailing after the mechanism?
- [ ] Is every sentence active, with the change as the actor — no "X becomes…", "Y gains…", or "…was added"?
- [ ] For a technical enabler, does the message name the capability it unlocks rather than only the surface it creates?
- [ ] For a definition change (rules, workflow, spec, docs), does each bullet name what the change defines and how far it reaches, rather than an effect that only appears once someone applies it?
- [ ] Does any bullet contain a route path, class name, method name, or HTTP header? If yes, rewrite or remove it.
- [ ] Would removing any bullet lose information a reviewer needs? If not, remove it.
- [ ] Is the whole message in the imperative, reading as the change about to be applied rather than a report of what was done?
- [ ] Are body lines wrapped at 72 characters?
- [ ] Is the total message under 30 lines?
