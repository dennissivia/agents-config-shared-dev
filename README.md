# Shared `.agents/` (software development workflow)

This repository is the **shared `.agents/` subfolder for software development workflows**. Drop these files into your repo’s `.agents/` (or `.agents/shared/`) folder. The structure follows `AGENTS_CODING_SPEC.md`.

## What Lives Here
- Development workflow package for coding-focused repos (`10–69`).
- Numeric prefixes set read order: a repo-local `00_index.md` dispatches; `01–09` are reserved for a separate global package; `10–69` are the workflow stages in this repo.
- `00_index.md` should explain precedence and how host repos can add `shared/`, `vendor/`, or `local/` layers, loading any global package before this one.

## Workflow Entry and Stages
- `10_dev_workflow.md` — entrypoint for development tasks; follow phases in order.
- `11–19` series — cross-phase workflow structure and workspace/bootstrap rules.
- `20–27` series — stage-specific rules: analysis, implementation, refactor,
  quality assurance, change persistence, review, and integration/deployment.

## Target Layout (when installed)
```
.agents/
  00_index.md
  # optional: global package (`01_global_*.md` files) from the shared global repo
  10_dev_workflow.md
  11_project_structure_and_deliverable_lifecycle.md
  12_workspace_01_worktrees.md
  20_analysis_01_technical-decisions.md
  21_implementation_01_security.md
  21_implementation_02_configuration-and-properties.md
  21_implementation_03_dependency-management.md
  21_implementation_04_observability-and-monitoring.md
  21_implementation_05_bare_python_project_setup.md
  22_refactor_01_code-quality.md
  23_validate_01_quality-gates.md
  24_qa_01_testing-standards.md
  24_qa_02_testing-standards_spring-boot.md
  25_persist_01_change-persistence-workflow.md
  25_persist_02_commit-messages.md
  26_review_01_pull-requests.md
  27_integrate_01_post-merge-and-deployment.md
AGENTS_CODING_SPEC.md  (reference standard)
```

## Numbering Guide
- `00`: dispatcher only (must be loaded first)
- `01–09`: reserved for global rules (communication, safety, boundaries, context) from the global package
- `10–19`: workflow entry, lifecycle, and workspace/bootstrap rules
- `20–69`: workflow rules (analysis, implementation, quality assurance,
  change persistence, review, integration/deployment)
- `70–79`: experimental / WIP
- `80–99`: reserved

## Precedence and Layering
- Read files in lexical order by prefix; `00_index.md` defines the load order, loading globals (`01–09`) from the separate package first (if present), then this development workflow package (`10–69`).
- In host repos with multiple scopes (e.g., `shared/`, `vendor/`, `local/`), the closest `.agents/` folder to the code wins; use `00_index.md` to declare load order.
- Keep `.agents/` tracked in git and avoid renaming/renumbering unless the structure changes; prefer adding new topic files.
- Do not use custom names like `.aicontract`; `.agents/` is the canonical location for AI guidance.
