# `npm`

Overrides and install-script approvals live in the root `package.json`; release-age policy lives in `.npmrc`. Commands with `--package-lock-only` read or write `package-lock.json` without `node_modules`.

## Detect

`npm` owns the project when `package-lock.json` or `npm-shrinkwrap.json` exists, or when `package.json` names `npm` in `packageManager` or `devEngines.packageManager`. The root `package.json` lists the member globs under `workspaces`; members appear in the listing below as links to their paths.

```bash
# List the direct dependencies of the root, including the workspace members.
# Mutates: nothing.
npm ls --package-lock-only
```

## Outdated

The table shows the current, wanted, and latest version of each outdated direct dependency and the member that declares it. It needs an installed `node_modules`; run the install command from "Update" first.

```bash
# List outdated direct dependencies of the root and every member.
# Mutates: nothing.
npm outdated --workspaces --include-workspace-root
```

## Inspect

```bash
# Print every published version with its publish date.
# Mutates: nothing.
npm view "$PACKAGE" time
```

```bash
# Print one version's dependency ranges, peer dependency ranges, engines, and deprecation notice.
# Mutates: nothing.
npm view "$PACKAGE@$VERSION" dependencies peerDependencies engines deprecated
```

## Dependents

`npm explain` shows each installed copy, its location, and every dependent with the range it declares (`foo@"^1.0.0" from bar@2.0.0`). It needs an installed `node_modules`; the second command reads the lockfile and shows resolved versions only.

```bash
# Show every copy of a package and the declared ranges that pull it in.
# Mutates: nothing.
npm explain "$PACKAGE"
```

```bash
# Show every locked copy of a package in the tree.
# Mutates: nothing.
npm ls "$PACKAGE" --all --package-lock-only
```

## Duplicates

`npm find-dupes` lists the duplicates that `npm dedupe` would collapse; duplicates whose ranges exclude each other do not appear there. The second command from "Dependents" shows every version of one package.

```bash
# List the duplicates that dedupe would collapse.
# Mutates: nothing.
npm find-dupes
```

`npm dedupe` takes no package names. To collapse the copies of one package onto a locked version, land it with the steps from "Update" in a member that declares it; the install also removes the nested copies whose dependents accept `$VERSION`. Cover a package that no member declares directly with an entry under "Override".

```bash
# Collapse every duplicated package across the tree.
# Mutates: package-lock.json.
npm dedupe --package-lock-only
```

## Constrain

Edit the declared range of `$PACKAGE` in the member's `package.json`: raise only the floor to `$VERSION` and keep the operator and any explicit upper bound (`^1.2.0` becomes `^$VERSION`, `>=1.2 <1.5` becomes `>=$VERSION <1.5`); an exact pin becomes `^$VERSION`.

## Update

No single command lands a package on exactly `$VERSION` and keeps its range. Run the first command, edit the range that it wrote back to the range from "Constrain", then run the second command; it keeps the locked `$VERSION` because the range admits it. For the root, omit `--workspace`.

```bash
# Lock exactly the target version in one member.
# Mutates: package.json and package-lock.json.
npm install "$PACKAGE@$VERSION" --save-exact --workspace "$MEMBER" --package-lock-only
```

```bash
# Re-resolve the lockfile after editing package.json.
# Mutates: package-lock.json.
npm install --package-lock-only
```

```bash
# Install exactly the locked versions.
# Mutates: node_modules.
npm ci
```

## Add

```bash
# Add a dependency with an explicit range to one member.
# Mutates: package.json and package-lock.json.
npm install "$PACKAGE@$RANGE" --workspace "$MEMBER" --package-lock-only
```

## Remove

```bash
# Remove a dependency from one member.
# Mutates: package.json and package-lock.json.
npm uninstall "$PACKAGE" --workspace "$MEMBER" --package-lock-only
```

## Override

Overrides live under `overrides` in the root `package.json`; `npm` ignores them in members. To test whether one is redundant:

1. Run `npm ls "$PACKAGE" --all --package-lock-only` and keep the output.
2. Delete the entry from `overrides`.
3. Run the re-resolve command from "Update".
4. Run `npm ls "$PACKAGE" --all --package-lock-only` again. The override is redundant when the resolved versions match step 1; otherwise restore the entry and re-resolve again.

## Patch

`npm` has no native patching; projects use `patch-package`, which keeps one diff per package in `patches/` named `$PACKAGE+$VERSION.patch` and applies them from a `postinstall` script. To remove a patch, delete its file; when no patch file remains, also delete the `postinstall` script that runs `patch-package`.

## Unused

Find unused dependencies by static analysis. Search the project's source, configuration, scripts, and CI files for each declared package name. A package that appears only in manifests and the lockfile is a candidate; a package that appears only in tests or build tooling is misplaced.

```bash
# List the project files that mention a package, outside ignored directories such as node_modules.
# Mutates: nothing.
rg --files-with-matches --fixed-strings "$PACKAGE"
```

Before removing a candidate, read its source under `node_modules/$PACKAGE`. Its `package.json` `bin`, `exports`, and plugin entry points show whether a script, a CLI, or a configuration file loads it without an import.

## Deprecated

`npm` has no command that lists deprecated packages in the tree. Query each version in question.

```bash
# Print the deprecation notice of one version; empty output means not deprecated.
# Mutates: nothing.
npm view "$PACKAGE@$VERSION" deprecated
```

## Hygiene

These keys go stale:

- `overrides` in `package.json`: the target leaves the graph or stops changing the resolution (see "Override").
- `allowScripts` in `package.json`: the package leaves the tree, a pinned `name@version` is upgraded, or the package drops its install script. `npm install-scripts prune --dry-run` lists these entries and `npm install-scripts prune` removes them; both need `node_modules`.
- `min-release-age-exclude` in `.npmrc`: the package no longer needs to bypass the age gate.
- `before` in `.npmrc`: the fixed date blocks releases the project needs.
