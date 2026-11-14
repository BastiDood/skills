# Yarn

Yarn keeps `resolutions` in the root `package.json` and keeps `packageExtensions`, catalogs, and release-age policy in `.yarnrc.yml`. Commands with `--mode update-lockfile` write `yarn.lock` and fetch missing packages into the cache without linking `node_modules` or `.pnp.cjs`.

## Detect

Yarn owns the project when `yarn.lock` has a `__metadata` entry, when `.yarnrc.yml` exists, or when `packageManager` starts with `yarn@`. The root `package.json` lists the member globs under `workspaces`.

```bash
# List every workspace member with its name and path.
# Mutates: nothing.
yarn workspaces list --json
```

## Outdated

Yarn has no non-interactive outdated command; `yarn upgrade-interactive` needs a terminal. Compare each locked version from the first command with the latest version from the second.

```bash
# List the locked direct dependencies of every member.
# Mutates: nothing.
yarn info --all
```

```bash
# Print the latest published version of a package.
# Mutates: nothing.
yarn npm info "$PACKAGE" --fields version
```

## Inspect

```bash
# Print every published version with its publish date.
# Mutates: nothing.
yarn npm info "$PACKAGE" --fields time
```

```bash
# Print one version's dependency ranges, peer dependency ranges, engines, and deprecation notice.
# Mutates: nothing.
yarn npm info "$PACKAGE@$VERSION" --fields dependencies,peerDependencies,engines,deprecated
```

## Dependents

The tree shows, for each member, every path to each resolved version; each dependent carries the range it declares as `(via npm:$RANGE)`. A package that already appears higher in the tree is not printed again.

```bash
# Show what depends on each resolved version of a package.
# Mutates: nothing.
yarn why "$PACKAGE" --recursive
```

## Duplicates

`yarn dedupe --check` lists descriptors with overlapping ranges that resolve to different versions and exits non-zero when there are any. Duplicates whose ranges exclude each other do not appear there; "Dependents" shows every version of one package.

```bash
# List the duplicates that dedupe would collapse.
# Mutates: nothing.
yarn dedupe --check
```

`yarn dedupe` accepts package names and globs. It moves each copy onto the highest locked version inside its range and never downgrades.

```bash
# Collapse the copies of one package onto the highest locked version inside each range.
# Mutates: yarn.lock and the package cache.
yarn dedupe "$PACKAGE" --mode update-lockfile
```

## Constrain

Edit the declared range of `$PACKAGE` in the member's `package.json`: raise only the floor to `$VERSION` and keep the operator and any explicit upper bound (`^1.2.0` becomes `^$VERSION`, `>=1.2 <1.5` becomes `>=$VERSION <1.5`); an exact pin becomes `^$VERSION`. A `catalog:` or `catalog:$NAME` range takes its value from `catalog` or `catalogs.$NAME` in `.yarnrc.yml`; edit that entry instead.

## Update

```bash
# Re-resolve matching package names across the project within their declared ranges.
# Mutates: `yarn.lock`, `node_modules` or `.pnp.cjs`, and the package cache.
yarn up --recursive "$PACKAGE"
```

For an exact target, run `set resolution` for each descriptor `$PACKAGE@npm:$RANGE` from "Dependents". After a "Constrain" edit, run the re-resolve command from "Override" first.

```bash
# Land one descriptor on exactly the target version, keeping the declared range.
# Mutates: yarn.lock, node_modules or .pnp.cjs, and the package cache.
yarn set resolution "$PACKAGE@npm:$RANGE" "npm:$VERSION"
```

```bash
# Install exactly the locked versions.
# Mutates: node_modules or .pnp.cjs, and the package cache.
yarn install --immutable
```

## Add

`yarn add` writes the given range to the manifest as is.

```bash
# Add a dependency with an explicit range to one member.
# Mutates: package.json, yarn.lock, and the package cache.
yarn workspace "$MEMBER" add "$PACKAGE@$RANGE" --mode update-lockfile
```

## Remove

```bash
# Remove a dependency from one member.
# Mutates: package.json, yarn.lock, and the package cache.
yarn workspace "$MEMBER" remove "$PACKAGE" --mode update-lockfile
```

## Override

Overrides live under `resolutions` in the root `package.json`; Yarn ignores them in members. To test whether one is redundant:

1. Run `yarn why "$PACKAGE" --recursive` and keep the output.
2. Delete the entry from `resolutions`.
3. Run the command below.
4. Run `yarn why "$PACKAGE" --recursive` again. The override is redundant when the resolved versions match step 1; otherwise restore the entry and run the command below again.

```bash
# Re-resolve the lockfile after editing package.json or .yarnrc.yml.
# Mutates: yarn.lock and the package cache.
yarn install --mode update-lockfile
```

## Patch

Patches live in `.yarn/patches/` (the `patchFolder` setting). A `patch:` range references each one, usually from a `resolutions` entry that `yarn patch-commit --save` writes. To remove a patch, delete its `resolutions` entry or restore the original range inside the `patch:` value, delete its file, then re-resolve with the command from "Override". Lockfile entries with `optional!builtin<compat/...>` are Yarn's own patches, not project patches.

## Unused

Find unused dependencies by static analysis. Search the project's source, configuration, scripts, and CI files for each declared package name. A package that appears only in manifests and the lockfile is a candidate; a package that appears only in tests or build tooling is misplaced.

```bash
# List the project files that mention a package, outside ignored directories such as node_modules.
# Mutates: nothing.
rg --files-with-matches --fixed-strings "$PACKAGE"
```

Before removing a candidate, read its manifest. With `nodeLinker: node-modules`, read the source under `node_modules/$PACKAGE`; with Plug'n'Play, which keeps packages as archives in the cache, print the fields with the command below. Its `bin`, `exports`, and plugin entry points show whether a script, a CLI, or a configuration file loads it without an import.

```bash
# Print the entry points of one version.
# Mutates: nothing.
yarn npm info "$PACKAGE@$VERSION" --fields bin,main,exports
```

## Deprecated

`yarn npm audit` reports deprecated packages next to security advisories and exits non-zero when it finds either. For any other version, read the `deprecated` field with the second command from "Inspect".

```bash
# List deprecated and vulnerable packages across every member, including transitive dependencies.
# Mutates: nothing.
yarn npm audit --all --recursive
```

## Hygiene

These keys go stale:

- `resolutions` in `package.json`: the target leaves the graph or stops changing the resolution (see "Override"), or a `patch:` entry names a version that leaves the lockfile.
- `packageExtensions` in `.yarnrc.yml`: the extended package leaves the graph or ships the field itself. The install reports these as `YN0068` and `YN0069`.
- `dependenciesMeta` in `package.json`: the package leaves the graph or no longer needs `built`, `optional`, or `unplugged`.
- `catalog` and `catalogs` in `.yarnrc.yml`: no member references an entry.
- `npmPreapprovedPackages` in `.yarnrc.yml`: the package no longer needs to bypass `npmMinimalAgeGate`.
- `npmAuditExcludePackages` and `npmAuditIgnoreAdvisories` in `.yarnrc.yml`: the package leaves the graph or the advisory no longer applies.
