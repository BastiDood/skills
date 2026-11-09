---
name: dependency-resolution-commands
description: Package-manager command playbooks for inspecting and changing resolved dependency versions. Use when listing outdated packages, reading registry metadata, tracing dependents or duplicates, deduplicating, constraining ranges, updating, adding, removing, overriding, or patching packages, finding unused or deprecated ones, or pruning stale manager configuration.
user-invocable: false
compatibility: Requires the latest releases of the selected package manager, `jq`, `rg`, and `curl`, plus any optional tool that a playbook names, such as pipdeptree or skopeo.
---

# Dependency Resolution Commands

Each playbook covers one package manager under the same headings to map a task to the same section in every ecosystem: "Detect", "Outdated", "Inspect", "Dependents", "Duplicates", "Constrain", "Update", "Add", "Remove", "Override", "Patch", "Unused", "Deprecated", and "Hygiene".

Filter a command's JSON output with `jq` when the command offers JSON. Otherwise, read its text output directly. A `# Mutates:` comment above each command names the files it writes. In Git Bash, set `MSYS_NO_PATHCONV=1` to pass path-like arguments to native tools unchanged.

## References

Read only the playbook for each package manager in scope. Detect the manager from the files that the repository already tracks rather than from the tools installed on the machine.

- A `pnpm-lock.yaml` or `pnpm-workspace.yaml` selects the [`pnpm` playbook](./references/pnpm.md).
- A `package-lock.json` or `npm-shrinkwrap.json` selects the [`npm` playbook](./references/npm.md).
- A `yarn.lock` with a `__metadata` entry selects the [Yarn playbook](./references/yarn.md). A `yarn.lock` without one belongs to Yarn Classic, which no playbook covers. Stop and tell the user.
- A `bun.lock` or `bun.lockb` selects the [Bun playbook](./references/bun.md).
- A `deno.json`, `deno.jsonc`, or `deno.lock` selects the [Deno playbook](./references/deno.md).
- A `uv.lock` or a `[tool.uv]` table in `pyproject.toml` selects the [`uv` playbook](./references/uv.md).
- A `requirements*.txt` or `requirements*.in` without a `uv` lockfile selects the [`pip` playbook](./references/pip.md).
- A `Cargo.toml` or `Cargo.lock` selects the [Cargo playbook](./references/cargo.md).
- A `Dockerfile*`, `*.Dockerfile`, `*.dockerfile`, `Containerfile*`, or Compose file selects the [container image playbook](./references/docker.md).
- A manager with none of these files has no playbook. Stop and tell the user.
