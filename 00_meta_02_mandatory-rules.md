# Mandatory Rules (Priority 1)

Read these before doing anything else. They are non-negotiable and point to the detailed contracts that follow. If a rule cites stack-specific tools (e.g., Java/Spring commands), use the equivalent for your stack or define a local override; do not ignore the intent of the rule.

1. **Prioritize security**: Protect secrets, avoid command injection, validate inputs (see `100_10_security.md`).
2. **Follow the collaborative workflow**: Move through the defined phases from implementation to PR (see `110_10_collaborative-workflow.md`).
3. **Run quality measures**: Format code and run tests before any git operation (see `120_10_quality-measures.md`).
4. **Follow Git/GitHub workflow**: Use feature branches, structured commits, and safe PR practices (see the `130_XX` files).
5. **Maintain AI context**: Re-read and update this contract whenever starting work or learning new preferences (see `200_10_ai-context-maintenance.md`).
