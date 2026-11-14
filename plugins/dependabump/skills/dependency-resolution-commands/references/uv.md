# `uv`

Commands for `uv` projects and workspaces that declare dependencies in `pyproject.toml` and resolve every member into one universal `uv.lock`.

## Detect

A `uv` project has a `pyproject.toml` with a `[project]` table and a `uv.lock` next to it. A workspace root adds a `[tool.uv.workspace]` table; all members share the root `uv.lock`.

```bash
# List workspace member names.
# Mutates: nothing.
uv workspace list
```

## Outdated

The tree reads `uv.lock` without re-locking and appends `(latest: vX)` to each outdated package; lines from a group or extra end in `(group: NAME)` or `(extra: NAME)`.

```bash
# Show direct dependencies of every member with their latest releases.
# Mutates: nothing.
uv tree --frozen --outdated --depth 1 --all-groups
```

Add `--package "$MEMBER"` to show one member.

## Inspect

`uv` has no registry-metadata command; the PyPI JSON API supplies it.

```bash
# Print every published version with its upload time and yank state.
# Mutates: nothing.
curl --silent --location "https://pypi.org/pypi/$PACKAGE/json" | jq '.releases | map_values({uploaded: .[0].upload_time_iso_8601, yanked: .[0].yanked})'
```

```bash
# Print the dependency ranges, Python requirement, and yank state of one version.
# Mutates: nothing.
curl --silent --location "https://pypi.org/pypi/$PACKAGE/$VERSION/json" | jq '.info | {version, requires_python, requires_dist, yanked, yanked_reason}'
```

`requires_dist` entries with an `extra == "..."` marker are optional extras.

## Dependents

The inverted tree shows each locked version of the package and the resolved versions of its dependents, not their declared ranges.

```bash
# Show the dependents of each locked version of a package.
# Mutates: nothing.
uv tree --frozen --universal --invert --package "$PACKAGE"
```

```bash
# Show the lines of uv.lock that reference a package, including the specifiers that workspace members declare.
# Mutates: nothing.
rg --fixed-strings "name = \"$PACKAGE\"" uv.lock
```

Member specifiers appear as `{ name = ..., specifier = ... }` under `[package.metadata]`; a registry dependent's range comes from "Inspect" on its locked version.

## Duplicates

The universal tree prints every locked version of every package at least once; a name that appears with two versions is a duplicate created by a marker fork.

```bash
# Show the full locked tree across every fork.
# Mutates: nothing.
uv tree --frozen --universal --all-groups
```

`uv` has no dedupe command; `--upgrade-package` with an exact version moves every locked copy of that package to `$VERSION`.

```bash
# Collapse every locked copy of a package onto one version.
# Mutates: uv.lock.
uv lock --upgrade-package "$PACKAGE==$VERSION"
```

## Constrain

Rewrite the specifier in `pyproject.toml` with the file-edit tool: raise only the floor to `$VERSION` and keep the operator and any explicit upper bound (`>=1.2,<1.5` becomes `>=$VERSION,<1.5`); an exact `==` pin becomes `>=$VERSION,<$NEXT_MAJOR`, where `$NEXT_MAJOR` increments the first non-zero component of `$VERSION` (`1.3.4` gives `2`, `0.4.2` gives `0.5`).

## Update

```bash
# Update the entire lockfile within the project constraints.
# Mutates: `uv.lock`.
uv lock --upgrade
```

The `==$VERSION` constraint applies to this one resolution only; `pyproject.toml` keeps its range and every other package keeps its locked version unless it conflicts.

```bash
# Land a package on exactly the target version.
# Mutates: uv.lock.
uv lock --upgrade-package "$PACKAGE==$VERSION"
```

```bash
# Make .venv match uv.lock for every member.
# Mutates: .venv.
uv sync --locked --all-packages
```

## Add

`--group "$GROUP"` targets a dependency group, `--dev` the `dev` group, and `--optional "$EXTRA"` an extra; with no flag, the package goes to `[project.dependencies]`.

```bash
# Add a dependency to one member.
# Mutates: pyproject.toml and uv.lock.
uv add --no-sync --package "$MEMBER" "$PACKAGE$RANGE"
```

## Remove

Pass the same section flag as "Add" to remove the package from that section.

```bash
# Remove a dependency from one member.
# Mutates: pyproject.toml and uv.lock.
uv remove --no-sync --package "$MEMBER" "$PACKAGE"
```

## Override

Overrides live in `override-dependencies` and constraints in `constraint-dependencies` of `[tool.uv]` in the workspace root `pyproject.toml`; `uv` ignores them in members.

To test whether an entry is redundant:

1. Run the "Update" command without a version (`uv lock --upgrade-package "$PACKAGE"`) and record the versions that the "Dependents" tree prints.
2. Delete the entry with the file-edit tool.
3. Run `uv lock --upgrade-package "$PACKAGE"` again and print the "Dependents" tree.
4. If the versions changed, restore the entry and run `uv lock`; otherwise leave it deleted.

Without `--upgrade-package`, `uv lock` keeps the locked version as a preference and hides the effect of the deletion.

## Patch

`uv` has no patch flow; a patched package is a `[tool.uv.sources]` entry that points at a Git fork or a local path.

```bash
# List the pyproject.toml files that declare sources.
# Mutates: nothing.
rg --fixed-strings --line-number "[tool.uv.sources]" --glob pyproject.toml
```

To remove a patch, delete its entry from `[tool.uv.sources]` with the file-edit tool, then run `uv lock --upgrade-package "$PACKAGE"`.

## Unused

Find unused dependencies by static analysis. A distribution's import name can differ from its package name (`PyYAML` imports as `yaml`). Read the installed package under the environment's `site-packages` (its `top_level.txt` or package directory) for the import name, then search the project for it. A package that appears only in manifests and the lockfile is a candidate; a package that appears only in tests or tooling is misplaced.

```bash
# List the project files that mention an import name, outside ignored directories such as .venv.
# Mutates: nothing.
rg --files-with-matches --fixed-strings "$IMPORT_NAME"
```

Before removing a candidate, read its source for entry points, plugins, or console scripts that a configuration file or script loads without an import.

## Deprecated

`uv audit` reports known vulnerabilities and deprecated, archived, or quarantined projects among the locked packages; it does not report yanked versions, which "Inspect" shows.

```bash
# Audit the locked packages.
# Mutates: nothing.
uv audit --frozen --preview-features audit-command
```

## Hygiene

`uv` prunes none of these keys and does not warn when they match nothing:

- `[tool.uv.sources]` entries: stale when no member declares the package.
- `override-dependencies`, `constraint-dependencies`, `exclude-dependencies`: stale when the package is no longer locked or the entry no longer changes the resolution.
- `[[tool.uv.index]]` entries with `explicit = true`: stale when no source references the index.
- `exclude-newer-package`, `no-build-package`, `no-binary-package`, `no-build-isolation-package`: stale when the package is no longer locked.
- `[tool.uv.workspace]` `exclude` globs: stale when they match no directory.
- `[tool.uv] dev-dependencies`: deprecated; its entries belong in `[dependency-groups] dev`.
