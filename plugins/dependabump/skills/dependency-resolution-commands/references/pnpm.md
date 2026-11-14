# `pnpm`

`pnpm` keeps overrides, catalogs, patches, build approvals, and release-age policy in `pnpm-workspace.yaml`. Every `pnpm` command first syncs the `configDependencies` declared there, which writes `pnpm-lock.yaml` and `node_modules/.pnpm-config` only when the declarations changed; the `# Mutates:` lines below leave that sync out.

## Detect

`pnpm` owns the project when `pnpm-lock.yaml` or `pnpm-workspace.yaml` exists, or when `package.json` names `pnpm` in `packageManager` or `devEngines.packageManager`. `pnpm-workspace.yaml` lists the member globs under `packages`.

```bash
# List every workspace member with its path.
# Mutates: nothing.
pnpm list --recursive --depth -1
```

## Outdated

The table shows the current, wanted, and latest version of each outdated direct dependency and the members that declare it.

```bash
# List outdated direct dependencies of every member.
# Mutates: nothing.
pnpm outdated --recursive
```

## Inspect

```bash
# Print every published version with its publish date.
# Mutates: nothing.
pnpm view "$PACKAGE" time
```

```bash
# Print one version's dependency ranges, peer dependency ranges, engines, and deprecation notice.
# Mutates: nothing.
pnpm view "$PACKAGE@$VERSION" dependencies peerDependencies engines deprecated
```

## Dependents

The reverse tree shows each resolved version and the chain of packages up to the members that depend on it, with resolved versions only. Read a dependent's declared range with the second command from "Inspect".

```bash
# Show what depends on each resolved version of a package.
# Mutates: nothing.
pnpm why "$PACKAGE" --recursive
```

## Duplicates

`pnpm dedupe --check` lists the duplicates that `pnpm dedupe` would collapse and exits non-zero when there are any; duplicates whose ranges exclude each other do not appear there, and "Dependents" shows every version of one package.

```bash
# List the duplicates that dedupe would collapse.
# Mutates: nothing.
pnpm dedupe --check
```

`pnpm dedupe` takes no package names. To collapse the copies of one package onto a locked version, run the targeted command from "Update". It fails with `ERR_PNPM_UPDATE_VERSION_ON_INDIRECT_DEP` for a package that no member declares directly, which needs an entry under "Override" instead.

```bash
# Collapse every duplicated package onto the newest version the lockfile already holds inside its range.
# Mutates: pnpm-lock.yaml.
pnpm dedupe --lockfile-only
```

## Constrain

Edit the declared range of `$PACKAGE` in the member's `package.json`: raise only the floor to `$VERSION` and keep the operator and any explicit upper bound (`^1.2.0` becomes `^$VERSION`, `>=1.2 <1.5` becomes `>=$VERSION <1.5`); an exact pin becomes `^$VERSION`. A `catalog:` or `catalog:$NAME` range takes its value from `catalog` or `catalogs.$NAME` in `pnpm-workspace.yaml`; edit that entry instead.

## Update

Use `--recursive` for the whole workspace, `--filter` for selected members, bare package names to narrow selection, and `--peer` for approved peer updates.

```bash
# Update dependencies within their declared ranges and save the resulting ranges.
# Mutates: `package.json`, `pnpm-workspace.yaml`, `pnpm-lock.yaml`, and `node_modules`.
pnpm up
```

For a direct dependency with a plain version range, the targeted update below locks `$VERSION` wherever that range admits it.

```bash
# Land a package on exactly the target version, keeping the declared ranges.
# Mutates: pnpm-lock.yaml.
pnpm update "$PACKAGE@$VERSION" --recursive --no-save --lockfile-only
```

```bash
# Install exactly the locked versions.
# Mutates: node_modules.
pnpm install --frozen-lockfile
```

## Add

```bash
# Add a dependency with an explicit range to one member.
# Mutates: package.json and pnpm-lock.yaml.
pnpm add "$PACKAGE@$RANGE" --filter "$MEMBER" --lockfile-only
```

## Remove

```bash
# Remove a dependency from one member.
# Mutates: package.json and pnpm-lock.yaml.
pnpm remove "$PACKAGE" --filter "$MEMBER" --lockfile-only
```

## Override

Overrides live under `overrides` in the root `pnpm-workspace.yaml`. To test whether one is redundant:

1. Run `pnpm why "$PACKAGE" --recursive` and keep the output.
2. Delete the entry from `overrides`.
3. Run the command below.
4. Run `pnpm why "$PACKAGE" --recursive` again. The override is redundant when the resolved versions match step 1; otherwise restore the entry and run the command below again.

```bash
# Re-resolve the lockfile after editing pnpm-workspace.yaml or package.json.
# Mutates: pnpm-lock.yaml.
pnpm install --lockfile-only
```

## Patch

Patches live under `patchedDependencies` in `pnpm-workspace.yaml`, which maps a `name@version` key to a patch file in `patches/`. To remove a patch, delete its key and its file, then re-resolve with the command from "Override".

## Unused

Find unused dependencies by static analysis. Search the project's source, configuration, scripts, and CI files for each declared package name. A package that appears only in manifests and the lockfile is a candidate; a package that appears only in tests or build tooling is misplaced.

```bash
# List the project files that mention a package, outside ignored directories such as node_modules.
# Mutates: nothing.
rg --files-with-matches --fixed-strings "$PACKAGE"
```

Before removing a candidate, read its source under `node_modules/$PACKAGE`. Its `package.json` `bin`, `exports`, and plugin entry points show whether a script, a CLI, or a configuration file loads it without an import.

## Deprecated

`pnpm outdated` marks a deprecated direct dependency as `Deprecated` in its latest column. For any other version, read the `deprecated` field with the second command from "Inspect".

```bash
# List outdated and deprecated direct dependencies of every member.
# Mutates: nothing.
pnpm outdated --recursive
```

## Hygiene

These keys in `pnpm-workspace.yaml` go stale:

- `overrides`: the target leaves the graph or stops changing the resolution (see "Override").
- `patchedDependencies`: the patched version leaves the lockfile; the next install fails with `ERR_PNPM_UNUSED_PATCH`.
- `catalog` and `catalogs`: no member references an entry.
- `allowBuilds`: the package leaves the graph or drops its build script.
- `minimumReleaseAgeExclude` and `trustPolicyExclude`: the listed package or version leaves the lockfile.
- `allowedDeprecatedVersions`: the package leaves the graph or is no longer deprecated.
- `packageExtensions`: the extended package leaves the graph or ships the field itself.
- `update.ignoreDeps`: the reason for holding the package back is gone.
