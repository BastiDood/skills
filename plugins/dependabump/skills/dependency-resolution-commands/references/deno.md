# Deno

Deno reads `jsr:` and `npm:` requirements from `imports` in `deno.json` and npm requirements from `package.json`, and resolves every member into one `deno.lock` at the workspace root. `$REGISTRY` below is `jsr` or `npm`; a JSR `$PACKAGE` is `@$SCOPE/$NAME`.

## Detect

Deno owns the project when `deno.json`, `deno.jsonc`, or `deno.lock` exists. The root `deno.json` lists the member directories under `workspace`; no Deno command lists the members.

## Outdated

The table shows the current version, the newest version inside the declared range ("Update"), and the latest version of each outdated direct dependency. Without `--recursive`, it covers only the member in the current directory.

```bash
# List outdated direct dependencies of every member.
# Mutates: nothing.
deno outdated --recursive
```

## Inspect

Deno has no registry-metadata command; the npm registry and the JSR API supply it.

```bash
# Print every published version of an npm package with its publish date.
# Mutates: nothing.
curl --silent "https://registry.npmjs.org/$PACKAGE" | jq '.time'
```

```bash
# Print one npm version's dependency ranges, peer dependency ranges, engines, and deprecation notice.
# Mutates: nothing.
curl --silent "https://registry.npmjs.org/$PACKAGE/$VERSION" | jq '{dependencies, peerDependencies, engines, deprecated}'
```

```bash
# Print every published version of a JSR package with its publish date and yank state.
# Mutates: nothing.
curl --silent "https://jsr.io/$PACKAGE/meta.json" | jq '.versions'
```

```bash
# Print one JSR version's dependency ranges.
# Mutates: nothing.
curl --silent "https://api.jsr.io/scopes/$SCOPE/packages/$NAME/versions/$VERSION/dependencies" | jq '.[] | {kind, name, constraint}'
```

## Dependents

The output reads `deno.lock` without the network and prints one block per resolved version, with every path from a member's direct dependency down to the package. The first segment of a path carries the range that the member declares; the later segments carry resolved versions only. Read a transitive dependent's declared range with "Inspect".

```bash
# Show what depends on each resolved version of a package.
# Mutates: nothing.
deno why "$PACKAGE"
```

## Duplicates

Deno has no dedupe command and no command that lists every duplicated package. "Dependents" shows every version of one package; an entry under "Override" collapses every copy of an npm package onto one version.

## Constrain

Edit the declared range of `$PACKAGE` in the member's `deno.json` `imports` value, after the last `@` (`jsr:@std/fmt@^1.0.2`), or in its `package.json`: raise only the floor to `$VERSION` and keep the operator and any explicit upper bound (`^1.2.0` becomes `^$VERSION`, `>=1.2 <1.5` becomes `>=$VERSION <1.5`); an exact pin becomes `^$VERSION`. A `catalog:` or `catalog:$NAME` range takes its value from `catalog` or `catalogs.$NAME` in the root `deno.json`; edit that entry instead.

## Update

No Deno command or edit sequence lands a package on exactly `$VERSION` under a range. Changing a requirement drops its locked entry; the next resolution takes the newest version that matches. The first command writes the exact requirement `$VERSION` in every member that declares the package. To keep a range instead, apply the edit from "Constrain" and run the second command, which locks the newest version inside it.

```bash
# Pin a package to exactly the target version in every member.
# Mutates: deno.json or package.json, and deno.lock.
deno outdated --update --recursive "$PACKAGE@$VERSION"
```

```bash
# Re-resolve the lockfile after editing deno.json or package.json.
# Mutates: deno.lock.
deno install --lockfile-only
```

```bash
# Install exactly the locked versions.
# Mutates: the global module cache, and node_modules when the project uses one.
deno install --frozen-lockfile
```

## Add

`deno add` edits the config file of the member in the current directory. It writes npm packages to `package.json` when the member has one and everything else to `imports` in `deno.json`.

```bash
# Add a dependency with an explicit range to the member in the current directory.
# Mutates: deno.json or package.json, and deno.lock.
deno add "$REGISTRY:$PACKAGE@$RANGE" --lockfile-only
```

## Remove

```bash
# Remove a dependency from the member in the current directory.
# Mutates: deno.json or package.json, and deno.lock.
deno remove "$REGISTRY:$PACKAGE" --lockfile-only
```

## Override

Overrides live under `overrides` in the root `package.json` and apply to npm packages only; JSR packages have no override mechanism. To test whether one is redundant:

1. Run `deno why "$PACKAGE"` and keep the output.
2. Delete the entry from `overrides`.
3. Run the re-resolve command from "Update".
4. Run `deno why "$PACKAGE"` again. The override is redundant when the resolved versions match step 1; otherwise restore the entry and re-resolve again.

## Patch

Deno has no diff-based patching. The `links` array in the root `deno.json` replaces a JSR or npm package with a local directory that holds a modified copy. To remove one, delete its path from `links`, then run the re-resolve command from "Update".

## Unused

Find unused dependencies by static analysis. Search the project's source, configuration, scripts, and CI files for each declared package name. A package that appears only in manifests and the lockfile is a candidate; a package that appears only in tests or build tooling is misplaced.

```bash
# List the project files that mention a package, outside ignored directories such as node_modules.
# Mutates: nothing.
rg --files-with-matches --fixed-strings "$PACKAGE"
```

Before removing a candidate, read its source under `node_modules/$PACKAGE` when the project uses one, or in the module cache that the command below locates. Its `bin` and `exports` show whether a task, a CLI, or a configuration file loads it without an import.

```bash
# Print the locations of the module cache and the npm cache.
# Mutates: nothing.
deno info
```

## Deprecated

Deno prints "The following packages are deprecated" only for npm packages that it sets up in a local `node_modules`. Query each version in question: read the `deprecated` field with the second command from "Inspect" for npm, or the `yanked` field with the third command from "Inspect" for JSR.

## Hygiene

These keys go stale:

- `overrides` in `package.json`: the target leaves the graph or stops changing the resolution (see "Override").
- `imports` in `deno.json`: no module imports the specifier (see "Unused").
- `scopes` in `deno.json`: no module under the scope imports the remapped specifier.
- `links` in `deno.json`: the local copy is no longer needed or its directory is gone.
- `catalog` and `catalogs` in `deno.json`: no member references an entry.
- `allowScripts` in `deno.json`: the package leaves the graph or drops its lifecycle script.
- `minimumDependencyAge.exclude` in `deno.json`: the package no longer needs to bypass the age gate.
