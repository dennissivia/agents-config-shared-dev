# Dependency Management

## Adding Dependencies

Before adding any dependency, verify all three and report the result:

- **Current**: it is on (or near) its latest stable release. Check the registry
  for the latest version and use it unless there is a documented reason not to.
- **No known vulnerabilities**: it is free of known CVEs / advisories. Check
  `npm audit`, GitHub advisories, OSV, Snyk, or an equivalent source, and confirm
  the dependency itself is not among the findings.
- **Actively maintained**: it has a recent release and visible upstream activity.
  Do not add abandoned or stale packages.

Document why each addition is needed. Prefer a small first-party implementation
over a new dependency when that is reasonable, and keep dependencies up to date.

## Dependency Updates
- Use simple commit messages for routine updates; structured commits only when an update enables a new feature.
- Run tests (unit + integration as relevant) after updating dependencies.
