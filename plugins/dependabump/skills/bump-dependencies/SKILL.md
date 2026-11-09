---
name: bump-dependencies
description: Use when upgrading project dependencies to their latest compatible versions with researched changelogs, duplicate-free lockfiles, and "Safe" or "Hold" verdicts.
disable-model-invocation: true
compatibility: Requires the selected package managers and the GitHub CLI (`gh`).
---

# Bump Dependencies

A bump is compatible only when it stays within the requested severity and adds no second version of any transitive package. Research the changelogs of every outdated package, assess each change against the codebase, and give every group a "Safe" or "Hold" verdict. Cleanup at the end of each run keeps the dependency set as lean as cleaning first would.

## Workflow

1. Resolve the scope from the user's request: package managers (every manager that the repository tracks unless the user names some), workspace members, the severity filter, and any package allowlist. A severity filter and an allowlist intersect. Without a severity filter, select minor and patch bumps only. `0.x` minor releases may break. Treat a minor bump of a `0.x` package as major. Record the production version of any container image in scope when the user gives one. Confirm that the working tree has no uncommitted changes to let Git restore it. If it has any, stop and ask.
2. Load `Skill(dependabump:dependency-resolution-commands)`. Until the user approves, run only commands whose `# Mutates:` label is "nothing" or a write that the playbook says every command forces. Discover outdated direct dependencies with its "Detect" and "Outdated" commands, apply the scope, and choose each package's target ("Inspect"): its newest release within the requested severity, the project's release-age policy ("Hygiene"), and any explicit upper bound (a comparator, hyphen range, or X-range) that its declared range sets. A `^`, `~`, or `~=` ceiling follows the new floor and does not cap the target. Record the "Duplicates" listing as the baseline.
3. Group the packages that must move together. A package with no link forms its own group. For each group, name the member whose versions bound the others, choose the other members' targets to match it, and record the linking constraint:
   - **Release Set:** packages published together at matched versions from one repository, such as a framework's scoped packages.
   - **Peer Pair:** one package declares a peer range on the other, or pins a version of a runtime that the other ships, such as a browser automation library and the browser build it drives.
   - **Pin Owner:** one package pins another exactly, letting the pinned package move only when its owner allows it.
4. For each group, fill every placeholder in the [group research prompt](./prompts/research-group.md) and spawn one sub-agent with the filled prompt as its whole message. Launch every group at once and wait for all of them. When a researcher corrects its grouping, regroup the declared packages and research the changed groups again.
5. Check each group against the lockfile with the playbook's "Dependents" and "Inspect" commands:
   - **Shared ranges:** for every entry in the group's "Dependency Changes", and for every locked package that depends on a member, check whether one version satisfies every declared range. A set of ranges that no single version satisfies adds a duplicate. Also compare the groups with each other: two groups can move one shared package to incompatible ranges.
   - **Overrides and patches:** list every override and patch that targets a member, one of its dependencies, or one of its dependents ("Override", "Patch"). Note any that the target version breaks.
6. Classify every group with the "Classification" criteria. Assign every label yourself to have one judge calibrate every row.
7. Fill the [bump report template](./assets/report-template.md) and present it, with one row per group. Order the rows within each section by "Risk", then "Impact", then package name. Omit empty sections. Stop for the user's approval and answers to the "Decisions". When nothing is outdated or the user approves no group, continue with cleanup.
8. Apply the approved groups:
   - Make the override and patch changes that the findings and the user's answers call for.
   - Constrain each approved member to its approved version ("Constrain").
   - Land each approved member on exactly its approved version with the playbook's exact "Update" form, or with its targeted form where a playbook has no exact form.
   - Make the code changes that the findings and the user's answers call for.
   - Collapse new duplicates with the playbook's scoped "Duplicates" commands that add no override. Install the result as "Update" shows. Then compare the "Duplicates" listing with the baseline, confirm that every approved member sits at exactly its approved version, check in the lockfile diff that no package moved beyond the approved members and their reported "Dependency Changes", and run the project's existing type-check, lint, and test scripts.
   - Present the changes and the check results. Stop for the user's review unless the user consented to autonomous refactors. Either way, a new duplicate, a member off its approved version, an unreported version move, or a check failure that the approved changes do not fix stops the run until the user resolves it. When the user rejects a group, restore the working tree with Git (including the untracked files that the run created) and apply the remaining groups again. Leave commits to the user.
9. Unless the user opted out of cleanup, run `Skill(dependabump:clean-dependencies)` on the same package managers and workspace members, with every approved member as a package to keep at its approved version and this run's uncommitted changes as expected.

## Classification

### Risk

Rate what the bump does to our code:

| Level      | Codebase Impact                                                 | Breakage Detection                                      | Fix                                                                 |
| ---------- | --------------------------------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------- |
| Trivial    | Nothing changes                                                 | Nothing to catch                                        | None                                                                |
| Mechanical | Stops compiling or linting                                      | Type checker, linter, tests                             | Rote edits (renames, new signatures, lint fixes)                    |
| Disruptive | Still runs, but behaves differently                             | Usually nothing. Tests that mock the dependency miss it | A setting or pin that keeps current behavior, or a product decision |
| Breaking   | A contract that we use is removed with no setting to restore it | Type checker or runtime failure                         | A redesign or replacement that needs engineering judgment           |
| Unknown    | Research left gaps                                              | Not applicable                                          | More research, or a decision from the user                          |

### Impact

Rate the blast radius: "None" (only transitive or unused code paths), "Local" (one module), or "Broad" (several modules, shared configuration, or the runtime version).

<impact_examples>

| Scenario                                                                                                   | Impact | Rationale                                           |
| ---------------------------------------------------------------------------------------------------------- | ------ | --------------------------------------------------- |
| A bumped package reaches the project only through another dependency, and no finding touches project code. | None   | Nothing that the project owns changes.              |
| Every affected call site sits in one feature module.                                                       | Local  | One module contains the change and its review.      |
| A repo-wide lint rule or a runtime bump changes every module.                                              | Broad  | Shared configuration carries the change everywhere. |

</impact_examples>

<risk_examples>

| Scenario                                                                                                                 | Risk       | Rationale                                                                                                                     |
| ------------------------------------------------------------------------------------------------------------------------ | ---------- | ----------------------------------------------------------------------------------------------------------------------------- |
| A patch release fixes a parser bug in a function that the code calls without changing any API or default.                | Trivial    | Needing no edit rules out "Mechanical".                                                                                       |
| A minor release adds a rule to the linter's recommended preset that reports five errors.                                 | Mechanical | Not "Disruptive": the linter finds every site, each with a rote fix.                                                          |
| A type-stub release tightens a return type, breaking type checks at eighteen call sites.                                 | Mechanical | Routing each read through an existing helper fixes every site without changing runtime behavior.                              |
| An SDK release switches its default API version. The code compiles unchanged. The tests mock the client.                 | Disruptive | Undetected by any check, the change is not "Mechanical". Pinning the previous API version keeps current behavior.             |
| A runtime upgrade stops populating an HTTP status text that the UI displays, with no setting to restore it.              | Disruptive | The code still runs, ruling out "Breaking". With no way to keep current behavior, it is a "Hold" with a "Decisions" question. |
| A client library removes the upload helper that the code calls. Its replacement requires restructuring how files stream. | Breaking   | Not "Mechanical": the fix needs design choices.                                                                               |
| A release has no changelog or release notes, and the diff between tags is too large to audit.                            | Unknown    | The evidence cannot support any other level.                                                                                  |

</risk_examples>

### Verdict

Decide whether to bump:

- **Hold:** a member's target exceeds the requested severity; "Risk" is "Breaking" or "Unknown"; "Risk" is "Disruptive" with no setting that keeps current behavior; the group adds a duplicate; the target breaks an override, a patch, or a declared engine or runtime constraint; or the target moves a container image past the production version that the scope records.
- **Safe:** no "Hold" condition applies. The rationale names the refactor or the setting that keeps current behavior.

<verdict_examples>

| Scenario                                                                                                       | Verdict | Rationale                                                                                                                           |
| -------------------------------------------------------------------------------------------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| A patch release fixes a parser bug, with every range in its "Dependency Changes" admitting the locked version. | Safe    | "Trivial", with no new version in the lockfile.                                                                                     |
| A minor release raises its floor on a shared utility past the range that another locked dependent declares.    | Hold    | With no single version satisfying both ranges, the bump adds a second copy of the utility.                                          |
| A minor release adds three new transitive packages, none of them already locked.                               | Safe    | The lockfile growth is a red flag, but nothing is duplicated. The rationale names the new packages and the feature that needs them. |
| An SDK release changes its default API version and offers a setting that restores the old one.                 | Safe    | "Disruptive" with a setting that keeps current behavior. The rationale names the setting.                                           |
| A database image release moves to the next major version while production runs the current one.                | Hold    | Production would run a different major version than the one tested.                                                                 |

</verdict_examples>

The rationale must account for added packages in a group's "Dependency Changes" as a red flag, not a verdict on its own. Every "Hold" whose "Risk" is "Disruptive" or "Unknown", and every "Hold" that an override or a patch update could resolve, gets a question in the "Decisions" section.
