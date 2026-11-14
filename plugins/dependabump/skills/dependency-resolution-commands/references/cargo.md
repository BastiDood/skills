# Cargo

Cargo resolves the crates of a whole workspace into one `Cargo.lock` at the workspace root. `--locked` makes a command fail instead of writing a missing or stale `Cargo.lock`.

## Detect

Cargo owns a directory that contains `Cargo.toml`; a `[workspace]` table in the root manifest lists the members. `[workspace.dependencies]` holds requirements that members inherit with `workspace = true`. The command prints the workspace members and their declared dependencies with requirement, kind, and target.

```bash
# List workspace members and their declared dependencies.
# Mutates: nothing.
cargo metadata --format-version 1 --no-deps --locked | jq '.packages[] | {name, manifest_path, dependencies: [.dependencies[] | {name, req, kind, rename, target}]}'
```

## Outdated

Each `Updating` or `Unchanged` line with `(available: vX)` names a package that has a newer release than the one it resolves to; direct dependencies also show the newest incompatible release. The output goes to stderr and the exit status is 0 in either case.

```bash
# List locked packages that have newer releases.
# Mutates: nothing.
cargo update --dry-run --verbose
```

## Inspect

The crates.io API allows one request per second. The first command prints every published version with its publish time, yank state, and minimum Rust version; the second prints the dependency requirements of one version.

```bash
# List the published versions of a crate.
# Mutates: nothing.
curl --silent "https://crates.io/api/v1/crates/$PACKAGE" | jq '.versions[] | {num, created_at, yanked, rust_version}'
```

```bash
# List the dependency requirements of one crate version.
# Mutates: nothing.
curl --silent "https://crates.io/api/v1/crates/$PACKAGE/$VERSION/dependencies" | jq '.dependencies[] | {crate_id, req, kind, optional}'
```

## Dependents

The inverse tree prints every path from the workspace to the package, without requirements. Qualify the spec as `$PACKAGE@$VERSION` when several versions are locked. Read a dependent's requirement from its manifest when it is a workspace member, or from the "Inspect" dependencies query when it is a crates.io crate.

```bash
# Show every path from the workspace to one package.
# Mutates: nothing.
cargo tree --workspace --target all --locked --invert "$PACKAGE"
```

## Duplicates

The command prints each package locked at more than one version, with the inverse tree of each version. One `cargo update --precise` moves one locked copy onto a locked version; it succeeds only when every dependent of that copy accepts `$VERSION`.

```bash
# List packages locked at more than one version.
# Mutates: nothing.
cargo tree --workspace --target all --locked --duplicates
```

```bash
# Move one locked copy of a package onto another locked version.
# Mutates: Cargo.lock.
cargo update "$PACKAGE@$LOCKED" --precise "$VERSION"
```

## Constrain

In the table that declares the crate, raise only the floor of the requirement to `$VERSION` and keep its operator and any explicit upper bound: `^1.2` becomes `^$VERSION`, `>=1.2, <1.5` becomes `>=$VERSION, <1.5`, and an exact `=1.2.3` pin becomes `^$VERSION`. A member entry with `workspace = true` takes its requirement from the root `[workspace.dependencies]` table; edit it there. `cargo add` re-resolves `Cargo.lock` after writing the manifest; make the edit with a file edit instead.

## Update

```bash
# Update the entire lockfile within the workspace requirements.
# Mutates: `Cargo.lock`.
cargo update
```

A targeted update fails when a dependent excludes `$VERSION`.

```bash
# Lock one package at exactly the target version.
# Mutates: Cargo.lock.
cargo update "$PACKAGE" --precise "$VERSION"
```

```bash
# Download every crate the lockfile records.
# Mutates: nothing.
cargo fetch --locked
```

## Add

`--dev` and `--build` select `[dev-dependencies]` and `[build-dependencies]`. The command also updates `Cargo.lock`.

```bash
# Add a dependency with a requirement to one member.
# Mutates: Cargo.toml and Cargo.lock.
cargo add --package "$MEMBER" "$PACKAGE@$RANGE"
```

## Remove

`--dev` and `--build` select the table. The command also deletes `[workspace.dependencies]` and `[patch]` entries that nothing references any more.

```bash
# Remove a dependency from one member.
# Mutates: Cargo.toml and Cargo.lock.
cargo remove --package "$MEMBER" "$PACKAGE"
```

## Override

Overrides live in `[patch.crates-io]` (or `[patch.URL]`) in the root `Cargo.toml` or in `.cargo/config.toml`; Cargo ignores `[patch]` in member manifests. To test whether an entry is redundant, run the "Dependents" command for its package, delete the entry, re-resolve with the command below, and run the "Dependents" command again; restore the entry and `Cargo.lock` when the versions or sources changed.

```bash
# Re-resolve one package after its [patch] entry is deleted.
# Mutates: Cargo.lock.
cargo update "$PACKAGE"
```

## Patch

Cargo has no diff-based patches; a patched crate is a `[patch]` entry (see "Override") that points at a `path` or `git` fork. To remove one, delete the entry and any vendored directory that nothing else uses, then run the "Override" re-resolve command.

## Unused

Find unused dependencies by static analysis. Code names a crate with hyphens replaced by underscores (`serde-json` becomes `serde_json`), or by the `package` rename in its manifest entry. Search the workspace for that name. A crate that appears only in `Cargo.toml` and `Cargo.lock` is a candidate; a `[dependencies]` crate that appears only in tests, benches, or examples is misplaced.

```bash
# List the workspace files that mention a crate name, outside ignored directories such as target.
# Mutates: nothing.
rg --files-with-matches --fixed-strings "$CRATE_NAME"
```

Before removing a candidate, read its source under the Cargo home's `registry/src` for proc macros, build scripts, or `links` entries that the build uses without a `use` statement.

## Deprecated

Crates.io has no deprecation flag; `cargo-audit` checks `Cargo.lock` against the RustSec database and prints vulnerabilities, unmaintained crates, and yanked versions. It writes `Cargo.lock` when the file is missing.

```bash
# Report unmaintained, yanked, and vulnerable locked crates.
# Mutates: nothing.
cargo audit
```

## Hygiene

- `[patch]` entries: stale when the patched package no longer appears in the graph; every resolving command then warns `patch ... was not used in the crate graph`.
- `[workspace.dependencies]` entries: stale when no member inherits them.
- `deny.toml` `skip`, `ignore`, and `exceptions` lists: stale when they name crates, advisories, or licenses that the graph no longer contains.
- `package.rust-version` and `rust-toolchain.toml`: stale when they are below the Rust version that the locked crates require.
