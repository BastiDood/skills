---
name: clean-dependencies
description: Use when pruning unused, deprecated, superseded, misplaced, or duplicated dependencies and stale package-manager configuration from a project.
compatibility: Requires the selected package managers and the GitHub CLI (`gh`).
---

# Clean Dependencies

Shrink the dependency set: every dependency that cleanup removes is one fewer package to maintain. Gather candidates with deterministic tools, confirm each judgment call in a parallel investigation, and apply every settled verdict without waiting for approval.

## Workflow

1. Resolve the scope from the request: package managers, workspace members, any package allowlist, any packages to keep at their versions, and any uncommitted changes to expect. When the working tree has other uncommitted changes, stop and ask before changing anything. Without named package managers, cover every manager that the repository tracks.
2. Load `Skill(dependabump:dependency-resolution-commands)`.
3. Collect candidates with the playbook commands.
   - **Unused:** declared dependencies that the "Unused" static analysis finds no use of.
   - **Deprecated:** declared dependencies with a registry deprecation notice, a yanked locked version, an unmaintained advisory, or an end-of-life image ("Deprecated").
   - **Superseded:** packages whose job the declared runtime or another existing dependency already does, such as a polyfill for a feature that the declared engine ships, two libraries that serve the same purpose, or a package whose deprecation notice names a successor. For JavaScript, check the [e18e module-replacements](https://github.com/e18e/module-replacements) manifests.
   - **Misplaced:** a runtime import declared only as a development dependency, or a build-only tool declared as a runtime dependency ("Unused").
   - **Duplicated:** packages that resolve to more than one version ("Duplicates"). Trace each extra version to the package that pins it ("Dependents").
   - **Stale Configuration:** overrides with no effect, patches for versions no longer locked, and "Hygiene" entries that the lockfile no longer references. Only these entries count. A "Hygiene" item that needs judgment, an override that caps a package below the newest version that its dependents' ranges allow ("Inspect"), and an entry whose removal would move a version, a policy, or a file format all stay. Skip lists that the manager already prunes automatically.
4. The playbook commands settle every "Stale Configuration" or "Duplicated" candidate during the apply step, without investigation.
5. Group the remaining candidates that must change together, such as a library with its type definitions, a host with its plugins, or two libraries that one replacement covers. Record why the members are grouped.
6. For each group, fill every placeholder in the [candidate investigation prompt](./prompts/investigate-candidates.md) and spawn one sub-agent with the filled prompt as its whole message. Launch every group at once and wait for all of them. When an investigator corrects its grouping, regroup and investigate the changed groups again.
7. Classify every package candidate with the "Classification" criteria. Assign every label yourself to have one judge calibrate every row.
8. Apply every "Remove", "Replace", "Move", and "Dedupe" verdict whose "Risk" is "Trivial", "Mechanical", or "Disruptive" with a setting that keeps current behavior. Delete every "Stale Configuration" entry, using the "Override" redundancy test for an entry that still matches a locked package. Use the playbook's "Add", "Remove", scoped "Duplicates", "Override", "Patch", and "Hygiene" commands, make the code edits that a replacement needs, and install the result as "Update" shows. Leave every other candidate unchanged. Never change the version of a package that the scope keeps. Then compare the "Duplicates" listing with the one that step 3 collected, confirm in the lockfile diff that no kept package moved and that no package rose above every version locked before the run, and run the project's existing type-check, lint, and test scripts. A new duplicate, a kept package that moved, a package that rose, or a failing check becomes a "Decisions" question that names the change it traces to. Leave commits to the user.
9. Fill the [cleanup report template](./assets/report-template.md) and present it. Order the package rows within each section by "Risk", then "Impact", then package name. Omit empty sections. Apply the user's answers to the "Decisions".

## Classification

### Risk

Rate what the change does to our code:

| Level      | Codebase Impact                                     | Breakage Detection              | Fix                                                          |
| ---------- | --------------------------------------------------- | ------------------------------- | ------------------------------------------------------------ |
| Trivial    | Nothing changes                                     | Nothing to catch                | Delete the declaration                                       |
| Mechanical | Stops compiling or linting until imports move       | Type checker, linter, tests     | Rote edits (import swaps, renamed calls)                     |
| Disruptive | Still runs, but the replacement behaves differently | Usually nothing                 | A setting that keeps current behavior, or a product decision |
| Breaking   | The replacement lacks a contract that we use        | Type checker or runtime failure | A redesign that needs engineering judgment                   |
| Unknown    | The evidence left gaps                              | Not applicable                  | More research, or a decision from the user                   |

### Impact

Rate the blast radius: "None" (nothing that the project owns references the package), "Local" (one module), or "Broad" (several modules or shared configuration).

### Verdict

Choose the action:

- **Remove:** nothing that the project owns uses the package.
- **Replace:** a maintained successor, an existing dependency, or the declared runtime covers the package's use.
- **Move:** the package belongs in another dependency section or group.
- **Dedupe:** the package resolves to more than one version. A scoped "Duplicates" command collapses every copy onto the highest locked version that all its dependents' ranges admit, without adding an override, changing any declared range, or lowering any declared dependency.
- **Keep:** no other verdict applies, including a false "Unused" candidate and a duplicate that only a raised range or an override would collapse.

<cleanup_classification_examples>

| Scenario                                                                                                                                        | Verdict | Risk       | Impact | Rationale                                                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------------------------------------- | ------- | ---------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| The search finds no use of a date library that no source file imports and no script invokes.                                                    | Remove  | Trivial    | None   | Deleting the declaration changes nothing and needs no fix.                                                                                                         |
| A deprecated HTTP client names a successor that keeps the same request API under a new import path. Three modules import the deprecated client. | Replace | Mechanical | Broad  | The type checker catches every old import, each fixed by a rote swap across several modules.                                                                       |
| A polyfill for an API that the declared engine ships has a subtly different timeout default.                                                    | Replace | Disruptive | Local  | The code keeps running, but requests time out at a different moment. Keep the old default explicitly. Without such a setting, this becomes a "Decisions" question. |
| Two versions of a URL parser are locked, and both dependents' ranges admit the newer one.                                                       | Dedupe  | Trivial    | None   | The scoped "Duplicates" command collapses both versions without changing a declared range. No project code imports the parser.                                     |
| An assertion library is declared in `dependencies`, but only test files import it.                                                              | Move    | Trivial    | None   | Moving it to the development section changes no runtime import.                                                                                                    |
| A deprecated ORM's successor drops the raw-query builder that one repository module calls.                                                      | Replace | Breaking   | Local  | With the successor lacking a contract that we use, the replacement needs a redesign and becomes a "Decisions" question.                                            |
| The search finds no import of a plugin that a configuration file may load by a computed name.                                                   | Keep    | Unknown    | None   | With the evidence unable to rule out a dynamic load, the removal becomes a "Decisions" question.                                                                   |
| The search finds a CLI tool only in a CI workflow.                                                                                              | Keep    | Trivial    | None   | Use outside the source tree makes the report a false positive rather than a removal.                                                                               |

</cleanup_classification_examples>

Every "Breaking" candidate, every "Disruptive" candidate without a setting that keeps current behavior, and every "Unknown" candidate gets a question in the "Decisions" section.
