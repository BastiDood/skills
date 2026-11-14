# Bun

Bun reads `package.json` and resolves into the text lockfile `bun.lock`. The read commands below work from the lockfile without `node_modules`.

## Detect

Bun owns the project when `bun.lock` or `bun.lockb` sits next to the root `package.json`, when `bunfig.toml` exists, or when `packageManager` starts with `bun@`. The root `package.json` lists the member globs under `workspaces`, as an array or as the `packages` array of an object; members appear in the listing below as `name@workspace:path`.

```bash
# List the direct dependencies, including the workspace members.
# Mutates: nothing.
bun pm ls
```

## Outdated

The table shows the current version, the highest version inside the declared range ("Update"), the latest version, and the member of each outdated direct dependency; dev dependencies carry a `(dev)` suffix. `--filter '*'` alone skips the root.

```bash
# List outdated direct dependencies of the root and every member.
# Mutates: nothing.
bun outdated --filter './' --filter '*'
```

## Inspect

```bash
# Print every published version with its publish date.
# Mutates: nothing.
bun info "$PACKAGE" time
```

```bash
# Print one version's manifest, including its dependency ranges, peer dependency ranges, engines, and deprecation notice.
# Mutates: nothing.
bun info "$PACKAGE@$VERSION" --json
```

## Dependents

The reverse tree shows each resolved version and its dependents, each with the range it declares as `(requires $RANGE)`.

```bash
# Show what depends on each resolved version of a package.
# Mutates: nothing.
bun why "$PACKAGE"
```

## Duplicates

`bun dedupe --dry-run` lists the duplicates that `bun dedupe` would remove; duplicates whose ranges exclude each other do not appear there. The command below it shows every version of one package.

```bash
# List the duplicates that dedupe would remove.
# Mutates: nothing.
bun dedupe --dry-run
```

```bash
# List every resolved version of one package.
# Mutates: nothing.
bun pm ls "$PACKAGE"
```

`bun dedupe` takes no package names. No Bun command collapses only the copies of a named package onto a locked version. An entry under "Override" collapses every copy of the package onto one version, including copies whose declared range excludes it.

```bash
# Collapse every duplicated package across the lockfile.
# Mutates: bun.lock.
bun dedupe --lockfile-only
```

## Constrain

Edit the declared range of `$PACKAGE` in the member's `package.json`: raise only the floor to `$VERSION` and keep the operator and any explicit upper bound (`^1.2.0` becomes `^$VERSION`, `>=1.2 <1.5` becomes `>=$VERSION <1.5`); an exact pin becomes `^$VERSION`. A `catalog:` or `catalog:$NAME` range takes its value from `catalog` or `catalogs` in the root `package.json`, at the top level or inside `workspaces`; edit that entry instead.

## Update

Use `--recursive` for all workspace members or `--filter` for selected members.

```bash
# Update dependencies within their declared ranges and save the resulting ranges.
# Mutates: `package.json`, `bun.lock`, and `node_modules`.
bun update
```

For a targeted update, pin the member to `$VERSION` and re-resolve the lockfile. Restore the range from "Constrain" and re-resolve again. The second resolution keeps the locked `$VERSION` because the range admits it. Nested copies under other packages keep their locked versions.

```bash
# Re-resolve the lockfile after editing package.json.
# Mutates: bun.lock.
bun install --lockfile-only
```

```bash
# Install exactly the locked versions.
# Mutates: node_modules.
bun install --frozen-lockfile
```

## Add

`bun add` writes `dependencies` unless `--dev`, `--optional`, or `--peer` selects another section.

```bash
# Add a dependency with an explicit range to one member.
# Mutates: package.json and bun.lock.
bun add "$PACKAGE@$RANGE" --filter "$MEMBER" --lockfile-only
```

## Remove

```bash
# Remove a dependency from one member.
# Mutates: package.json and bun.lock.
bun remove "$PACKAGE" --filter "$MEMBER" --lockfile-only
```

## Override

Overrides live under `overrides` or `resolutions` in the root `package.json`; Bun ignores `resolutions` when `overrides` exists and supports only top-level package names. To test whether one is redundant:

1. Run `bun pm ls "$PACKAGE"` and keep the output.
2. Delete the entry.
3. Run the re-resolve command from "Update".
4. Run `bun pm ls "$PACKAGE"` again. The override is redundant when the resolved versions match step 1; otherwise restore the entry and re-resolve again.

## Patch

Patches live under `patchedDependencies` in the root `package.json`, which maps a `name@version` key to a patch file in `patches/`. To remove a patch, delete its key and its file, then run the re-resolve command from "Update".

## Unused

Find unused dependencies by static analysis. Search the project's source, configuration, scripts, and CI files for each declared package name. A package that appears only in manifests and the lockfile is a candidate; a package that appears only in tests or build tooling is misplaced.

```bash
# List the project files that mention a package, outside ignored directories such as node_modules.
# Mutates: nothing.
rg --files-with-matches --fixed-strings "$PACKAGE"
```

Before removing a candidate, read its source under `node_modules/$PACKAGE`. Its `package.json` `bin`, `exports`, and plugin entry points show whether a script, a CLI, or a configuration file loads it without an import.

## Deprecated

Bun has no command that lists deprecated packages in the tree; `bun install` warns only for the packages it downloads. Query each version in question.

```bash
# Print the deprecation notice of one version; an error with exit status 1 means not deprecated.
# Mutates: nothing.
bun info "$PACKAGE@$VERSION" deprecated
```

## Hygiene

These keys go stale:

- `overrides` and `resolutions` in `package.json`: the target leaves the graph or stops changing the resolution (see "Override").
- `patchedDependencies` in `package.json`: the patched version leaves the lockfile.
- `trustedDependencies` in `package.json`: the package leaves the graph or drops its lifecycle script. Defining the key replaces Bun's default trusted list.
- `catalog` and `catalogs` in `package.json`: no member references an entry.
- `install.minimumReleaseAgeExcludes` in `bunfig.toml`: the package no longer needs to bypass the age gate.
