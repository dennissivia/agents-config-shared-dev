# Specification: `.agents/` Folder Structure for AI Collaboration

## Purpose

This document defines the structure and conventions for using a `.agents/` folder to guide AI coding assistants (e.g., GitHub Copilot, Sourcegraph Cody, ChatGPT, etc.) in software repositories. It extends emerging conventions from `AGENTS.md` into a scalable, maintainable folder-based format suitable for multi-team and multi-repo setups.

## Naming Convention

* Use `.agents/` (plural, hidden folder) at the root of the repository.
* Do **not** use custom terms like `aicontract`, as `.agents/` is the most widely accepted, tooling-agnostic name.
* Use markdown (`.md`) files only.

## File Precedence and Dispatcher

There is no default file precedence across `.agents/` files. To establish interpretation order:

### Dispatcher File (Always First)

* Include a `00_index.md` at the top level of `.agents/`.
* This dispatcher defines folder loading order, rule precedence, and optional validation behavior.

### Numeric Prefix System

To ensure deterministic loading, prefix files numerically.

## Reserved Numbering Blocks

| Range   | Purpose                                                                               |
| ------- | ------------------------------------------------------------------------------------- |
| `00`    | **Dispatcher Only** – always loaded first; defines rule application order             |
| `01–09` | **Global Rules** – universal communication, boundaries, safety, etc.                  |
| `10–19` | **Workflow Entry / Package Indexes** – e.g., `10_dev-index.md`, `11_writing-index.md` |
| `20–69` | **Workflow Rules (Reusable Stages)** – implementation, validation, git, etc.          |
| `70–79` | **Experimental / WIP** – unstable, under-review, or prototype rules                   |
| `80–99` | **Reserved** – do not use unless explicitly adopted in future standards               |

This schema ensures clarity and compatibility across projects and packages.

## File Structure Examples

This structure can be used for both **simple setups** (single repository, basic rule files) and **more complex multi-package configurations** with shared cross-repo rule modules. The goal is flexibility: teams can adopt as little or as much structure as needed, while benefiting from clear file organization and agent-friendly loading logic.

### Simple Example with One Shared Workflow

```bash
.agents/
├── 00_index.md
├── shared/
│   ├── 01_dev_workflow.md
│   ├── 20_coding_standard.md
│   └── 21_git_rules.md
```

In this layout:

* `00_index.md` describes the interpretation sequence.
* `01_dev_workflow.md` outlines the agent's behavior expectations.
* Files `20_*.md` and `21_*.md` define reusable development rules.

### Complex Example with Multiple Packages

For large or cross-repo projects:

```bash
.agents/
├── 00_index.md                   # Dispatcher file
├── shared/
│   ├── global/
│   │   ├── 01_global.md
│   │   ├── 02_communication_guidelines.md
│   │   ├── 03_safety_nets.md
│   │   └── 04_hard_stops_and_boundaries.md
│   ├── dev/
│   │   ├── 10_dev-index.md
│   │   ├── 20_planning.md
│   │   ├── 30_implementation.md
│   │   └── 50_git.md
│   ├── techreview/
│   │   ├── 11_techreview-index.md
│   │   ├── 21_context.md
│   │   ├── 22_scope-review.md
│   │   └── 50_validation.md
│   └── writing/
│       ├── 12_writing-index.md
│       ├── 20_prompt-guidelines.md
│       └── 50_publishing-checklist.md
├── vendor/
│   └── 55_toolchain.md
├── local/
│   ├── 60_project-overrides.md
│   └── 70_custom-qa.md
```

Dispatcher content example:

```markdown
# Agent Index

Apply packages in this order:
1. shared/global/01_global.md
2. shared/dev/10_dev-index.md
3. shared/techreview/11_techreview-index.md
4. shared/writing/12_writing-index.md
5. vendor/
6. local/

Each index defines how to load its associated rules. Follow numeric order within each directory.
```

## Precedence Rules

* **Dispatcher-first**: `.agents/00_index.md` must be the entrypoint for all setups.
* **Global Rules**: `01–09` files define environment-agnostic or org-wide behavior (e.g. safety nets, communication, boundaries).
* **Package-specific rules** must live under `10–69`, grouped by workflow.
* **Experimental/Reserved** should be isolated and not loaded by default unless instructed.

## Tool Compatibility

* Many AI tools support `AGENTS.md`; few yet support `.agents/` officially.
* However, `.agents/` is an emerging community norm and aligns with discussions and proposals.
* Organizing your instructions this way is forward-compatible and improves maintainability.

## Best Practices

* Keep files modular (one topic per file).
* Document rule intent, scope, and application clearly.
* Use consistent prefixes and layers if collaborating across teams.
* Adopt `00_index.md` and reserved prefix blocks to ensure deterministic reading.

## Summary

To define structured, maintainable AI guidance:

* Use `.agents/` as the instruction folder.
* Split files by topic using numeric prefixes.
* Always include `00_index.md` to define interpretation.
* Use `01–09` for global cross-cutting rules like boundaries or communication.
* Organize by packages (e.g., `dev/`, `writing/`) with their own `10–19` index and `20–69` rule files.
* Support both simple and advanced configurations with consistent patterns.
