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

## Closing An Advisory Raises The Declared Floor

Where a dependency is declared as a range and resolved through a lockfile, closing
a security advisory means raising the lower bound of the declared range to the
first safe version. Updating only the lockfile is not a fix.

A lockfile records one resolution; the manifest is the contract. Leave the floor
where it was and any path that does not use this exact lockfile can still resolve
a vulnerable version that satisfies the range — a fresh install from a stale or
absent lockfile, a branch that has not merged, another environment installing
against the same manifest. The safe version is then a coincidence rather than a
requirement.

This applies to every ecosystem with fuzzy ranges and a lockfile — npm, Yarn and
pnpm, Bundler, Cargo, Poetry and uv, Go modules, Composer. In npm terms,
`^7.18.4` is a floor; `^7.18.0` with a patched lockfile is not.

- After the update, read the declared range back and confirm it cannot admit any
  version below the fix.
- Prefer the latest patch in the line over the minimum that closes the advisory,
  per the currency requirement above.
- Where a manifest declares exact versions instead of ranges, as Maven does, that
  version is itself the floor; overriding a managed version is the equivalent step.
- Where the advisory is on a transitive package whose owning dependency has not
  moved, do not force it past the owner's declared range. Record the advisory,
  its severity, the owning package and the follow-up trigger instead.
