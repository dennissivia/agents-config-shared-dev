# Project Setup (Python CLI projects)

These rules apply when initializing a new Python-based CLI project and align with the meta workflow.

- Follow the multi-phase workflow: plan the setup, then validate a minimal runnable skeleton before further features.
- Use the latest supported Python, manage deps with `uv`, and enforce lint/format via `ruff`; install tools via package managers only (no curl|bash scripts).
- Establish structure up front: `src/` for code, `tests/` for automated checks, `docs/` for documentation, `config/` for config/credentials, `data/` for inputs (with `data/playlists/` as the playlists home), and `bin/` for helper CLIs (prefer `bin/` over `scripts/`).
- Configure `pre-commit` hooks early (ruff, whitespace, EOF) and ensure they run cleanly before the first commit.
- Initialize git with a `.gitignore` that excludes `.aicontract/shared` while keeping `.keep` files so contract scaffolding is tracked.
- Require a minimal v0.1 milestone before the first commit: placeholder class + CLI stub, passing test, lint/format clean, and hooks verified.
