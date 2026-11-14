---
name: bump-dependencies
description: Use when upgrading project dependencies to their latest compatible versions with researched changelogs, duplicate-free lockfiles, and "Safe" or "Hold" verdicts.
disable-model-invocation: true
compatibility: Requires the selected package managers and the GitHub CLI (`gh`).
---

# Bump Dependencies

Research each candidate against the codebase and the lockfile. Recommend each group to bump now ("Safe") or later ("Hold").

## Workflow

1. Resolve the scope from the user's request: package managers (every manager that the repository tracks unless the user names some), workspace members, the severity filter, and any package allowlist. A severity filter and an allowlist intersect. Without a severity filter, select minor and patch bumps only. `0.x` minor releases may break. Treat a minor bump of a `0.x` package as major. Record the production version of any container image in scope when the user gives one. Confirm that the working tree has no uncommitted changes to let Git restore it. If it has any, stop and ask.
2. Load `Skill(dependabump:dependency-resolution-commands)`. Until the user approves, run only commands whose `# Mutates:` label is "nothing" or a write that the playbook says every command forces. Discover outdated direct dependencies with "Detect" and "Outdated". Use "Inspect" to choose the newest release within scope, the project's release-age policy ("Hygiene"), and any explicit upper bound (a comparator, hyphen range, or X-range). A `^`, `~`, or `~=` ceiling follows the new floor and does not cap the target. For each release-age exclusion, record its eligibility time and any eligible fallback. Record the distinct resolved versions of each package as the duplicate baseline. Use the lockfile or equivalent resolution record, with "Duplicates" and "Dependents" where applicable.
3. Group the packages that must move together. A package with no link forms its own group. For each group, name the member whose versions bound the others, choose the other members' targets to match it, and record the linking constraint:
   - **Release Set:** packages published together at matched versions from one repository, such as a framework's scoped packages.
   - **Peer Pair:** one package declares a peer range on the other, or pins a version of a runtime that the other ships, such as a browser automation library and the browser build it drives.
   - **Pin Owner:** one package pins another exactly, letting the pinned package move only when its owner allows it.
4. For each group, fill every placeholder in the [group research prompt](./prompts/research-group.md) and spawn one sub-agent with the filled prompt as its whole message. Launch every group at once and wait for all of them. When a researcher corrects its grouping, regroup the declared packages and research the changed groups again.
5. Check each group against the lockfile with the playbook's "Dependents" and "Inspect" commands:
   - **Shared ranges:** check each changed dependency or peer range and the ranges that locked dependents declare for each group member. Project the distinct resolved versions under the combined targets. A new duplicate is a count greater than both one and that package's starting count.
   - **Overrides and patches:** list every override and patch that targets a member, one of its dependencies, or one of its dependents ("Override", "Patch"). Note any that the target version breaks.
6. Classify every group with the "Classification" criteria. Assign every label yourself to have one judge calibrate every row.
7. Check which unapproved releases an ordinary update could select under the planned and unchanged ranges. Present the [bump report](./assets/report-template.md) for approval of exact targets and the narrowest constraint edits needed. Obtain answers to decisions before applying affected groups. If the user approves a subset, recheck its combined resolution and get approval for any revised plan. When nothing is outdated or the user approves no group, continue with cleanup.
8. Apply the approved groups:
   - Apply the approved range, override, and patch changes first ("Constrain", "Override", "Patch").
   - Run the ordinary range-respecting "Update" in the working tree when its constraints and selectors fit the approved result. Otherwise follow the playbook's targeted procedure. If the user requires an ordinary update, surface any scope conflict in "Decisions" before applying.
   - Check the resolution before making code changes. For a targeted update, first collapse new duplicates with the playbook's scoped "Duplicates" commands that add no override. Use the starting baseline and the same per-package duplicate rule. Check each direct declaration in each member against its approved target or starting version. Review the working-tree diff for scope and trace transitive moves back to the approved groups.
   - If the update or dedupe left dependencies to install, install from the checked resolution as "Update" shows. Make the approved code changes and run the project's existing type-check, lint, and test scripts.
   - Present the changes and check results. Stop for the user's review unless the user consented to autonomous refactors. An update or install failure, a new duplicate, an unapproved change, or a failed project check that the approved changes do not fix stops the run before cleanup. When the user rejects a group, restore the working tree with Git (including the untracked files that the run created) and apply the remaining groups again. Leave commits to the user.
9. Unless the user opted out of cleanup, run `Skill(dependabump:clean-dependencies)` on the same scope. Pass the approved and held direct versions, the constraints that keep ordinary updates within scope, and this run's expected uncommitted changes.

## Classification

### Risk

Rate what the bump does to our code:

| Level      | Codebase Impact                                                 | Breakage Detection                                      | Fix                                                                 |
| ---------- | --------------------------------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------- |
| Trivial    | Nothing changes                                                 | Nothing to catch                                        | None                                                                |
| Mechanical | Requires rote edits to keep current behavior                    | Type checker, linter, tests                             | Renames, new signatures, lint fixes                                 |
| Disruptive | Still runs, but behaves differently                             | Usually nothing. Tests that mock the dependency miss it | A setting or pin that keeps current behavior, or a product decision |
| Breaking   | A used contract changes and the migration needs a design choice | Type checker or runtime failure                         | A redesign or replacement that needs engineering judgment           |
| Unknown    | Evidence leaves the local effect unresolved                     | Not applicable                                          | Research the missing evidence                                       |

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

| Scenario                                                                                                                 | Risk       | Rationale                                                                                                         |
| ------------------------------------------------------------------------------------------------------------------------ | ---------- | ----------------------------------------------------------------------------------------------------------------- |
| A patch release fixes a parser bug in a function that the code calls without changing any API or default.                | Trivial    | The project can take the fix as-is.                                                                               |
| A type-stub release tightens a return type, breaking type checks at eighteen call sites.                                 | Mechanical | Routing each read through an existing helper fixes every site without changing runtime behavior.                  |
| A runtime upgrade stops populating an HTTP status text that the UI displays, with no setting to restore it.              | Disruptive | The UI still runs and loses the text. Keeping the upgrade requires a product decision about what the UI displays. |
| A client library removes the upload helper that the code calls. Its replacement requires restructuring how files stream. | Breaking   | The replacement requires a new buffering strategy.                                                                |
| A release has no changelog or release notes, and the diff between tags is too large to audit.                            | Unknown    | The evidence cannot support any other level.                                                                      |

</risk_examples>

### Verdict

| Verdict | Criteria                                                                                                                                                                                                                                         |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Safe    | Bump now. The group fits the requested scope, release policy, and dependency constraints. The project can take it as-is, with a mechanical edit, or with a setting that preserves behavior.                                                      |
| Hold    | Bump later. The group needs a product or engineering decision, has a material evidence gap, exceeds scope or release policy, adds a duplicate, or conflicts with a peer, override, patch, engine, runtime, or recorded production image version. |

<verdict_examples>

| Scenario                                                                                                                                       | Verdict | Rationale                                                                                                                    |
| ---------------------------------------------------------------------------------------------------------------------------------------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------- |
| A major upgrade in scope renames a helper used in one module. Its replacement takes the same arguments and returns the same result.            | Safe    | Rename the local call and run the module's tests. All changed dependency ranges admit the locked versions.                   |
| A minor upgrade of an import linter moves its lexer range from `^2.0.0` to `^3.0.0`. The test runner still requires `^2.0.0`.                  | Hold    | Only lexer `2.3.2` is locked. The linter would add 3.x alongside the test runner's 2.x. Revisit when the runner accepts 3.x. |
| An SDK release changes its default API version and offers a setting that restores the old one.                                                 | Safe    | Set the API version explicitly to preserve the client's behavior.                                                            |
| A client removes an upload helper that the project uses. The new streaming API adds retry control and requires choosing how to buffer uploads. | Hold    | Retry control is useful for large files. Choose the buffering behavior and verify retries and memory use before bumping.     |

</verdict_examples>
