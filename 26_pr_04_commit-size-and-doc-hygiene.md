# Commit Size and Documentation Hygiene

Rules for keeping commits reviewable and documentation clean.

---

## Commit Size — Hard Limit: 800 Lines Added

Before proposing a commit, count lines added:

```bash
git diff --cached --numstat | awk '{add+=$1} END {print "Lines added:", add}'
```

**If lines added exceed 800, a mandatory Extra Review is required** (see below).

This is a hard gate, not a soft suggestion. The extra review must complete before
composing the commit message.

### What counts toward the limit

- All staged additions across all files.
- Tests, boilerplate, and config all count.
- Generated files and lock files are excluded from the spirit of the check but included
  in the count — call them out explicitly if they inflate the number artificially.

---

## Extra Review: When 800 Lines Are Exceeded

Run this analysis and include the results in the commit message under an
"Extra review" section.

### Step 1 — Categorise the additions

Break the added lines down by category:

| Category | Examples |
|----------|----------|
| **Production code** | Services, controllers, domain models, hooks, components |
| **Tests** | Unit tests, integration tests, component tests |
| **Boilerplate / config** | Annotations, getters/setters, wiring, build config |
| **Documentation** | Comments, markdown, ADRs, migration docs |
| **Generated / data** | SQL migrations, seed data, i18n strings, lock files |

Report the approximate split (e.g. "55% tests, 30% production code, 15% config").

### Step 2 — Reusability and extraction review

Answer each question explicitly:

1. **Test setup duplication**: Is any setup code repeated across test methods that
   belongs in a `beforeEach` / `@BeforeEach` block or a shared fixture?
2. **Shared utilities**: Is logic duplicated across files that could be extracted into
   a shared helper, utility function, or base class?
3. **Test helpers**: Are there helper factories, builders, or stub constructors that
   are defined inline but used in multiple tests?
4. **Frontend tests**: Do tests re-declare mocks or render wrappers that could be
   centralised in a `renderWithProviders` helper or a shared `beforeEach`?

Fix extraction issues before committing. If deferring, note it explicitly.

### Step 3 — Could this be split?

Ask: "Does this logical unit do more than one separable thing?"

- If yes, consider splitting into two commits (or two PRs if size warrants).
- If no, proceed and document the rationale for the size.

---

## Documentation Hygiene

### Flag all doc changes

When staging documentation changes, call them out explicitly:

- New files in `docs/` — state the purpose and whether they are permanent or temporary.
- Modified ADRs — note what changed and whether it supersedes a prior decision.
- New planning docs — confirm whether they replace an existing doc or extend it.

### Versioned temp files are a smell

Files like `M2-implementation-plan-v2.md` alongside `M2-implementation-plan.md` are
a sign that an in-session workaround left a stale artifact. The repository is
version-controlled; there is no need for manual versioning via filename suffixes.

**Before committing, check for and remove:**

- Files with `-v2`, `-v3`, `-revised`, `-new`, `-old`, `-bak` suffixes.
- Duplicate planning docs where the newer file supersedes the older.
- Temp files created during exploration (e.g., `*-draft.md`, `*-temp.md`).

When removing, verify the content of the old file is either incorporated into the new
one or genuinely obsolete. Never silently delete content.

**If both files should be committed** (e.g., the old one is referenced elsewhere),
flag this to the user and ask for a decision instead of guessing.
