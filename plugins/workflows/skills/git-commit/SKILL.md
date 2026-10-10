---
name: git-commit
description: Use when incrementally organizing the Git staging area into vertically-sliced review checkpoints while preserving dependency order and prioritizing lower review effort until all relevant cohesive changes have become atomic commits.
compatibility: Requires Git (`git`).
---

# Smart Commit

Turn the current working-tree changes into a clear, reviewable sequence of checkpoints as conventional Git commits in topological order. Within that order, clear easy reviews first so later reviews can concentrate on complex integration and core logic. Incrementally stage one genuine work slice at a time, then let the user review the handoff and create the commit.

## Workflow

1. Inspect all staged and unstaged changes. Identify the cohesive slices of work that belong in separate commits.
   - Use genuine, independently understandable work slices. Do not split changes into overly granular commits merely because they can be separated.
   - Keep the repository in a consistent state after each proposed commit.
   - Identify dependencies between slices and estimate each slice's review effort from its behavioral complexity, required context, affected boundaries, and validation burden. Diff size alone does not determine review effort.
2. Select the next slice and stage all files and relevant subsets or hunks that belong to it.
   - Select only from slices whose prerequisites are already committed. Among those ready slices, prefer the lowest review effort; use narrative clarity to break comparable-effort ties.
   - Reassess the ready slices after each commit. Clear available low-effort reviews before complex integration or core logic, even when those complex slices are also ready. A prerequisite must still precede an easier dependent slice.
     - Example: review an independent documentation correction before a ready core-logic change. If an easy integration slice depends on that core-logic change, review the prerequisite first.
   - Include the complete slice, including its tests and documentation when applicable.
   - Use partial-file staging when a file contains changes for more than one slice.
   - Review the staged diff and confirm that it contains the intended slice and no unrelated changes.
3. Present a concise review handoff:
   - `Commit`: conventional title based only on the staged diff.
   - `Summary`: what changed and why it was necessary.
   - `Deferred`: relevant out-of-scope work and the later slice that resolves it. Omit when none.
   - The summary is reviewer context, not the optional commit body. Base it on the staged diff; use remaining changes only to place deferred work in the review line.
4. Ask the user to review the handoff and create the commit.
   - [Use the complete commit-message example when the staged slice needs a body or footer.](./references/example.txt)
   - Never run `git commit`. The user must create every commit unless explicitly delegated to you.
   - Stop and wait for the user after presenting the complete handoff.
5. After the user creates the commit, inspect the remaining changes, update dependencies and review-effort estimates, and return to Step 2. Continue until no pending changes remain.

## General Guidelines

- Use one commit when all pending changes form one cohesive work slice.
- Generate the proposed message only from the staged changes.
- Stage files and hunks only for the current work slice.
- Do not create commits on the user's behalf.
- Do not add advertisements or promotional footers.
- Avoid vague titles such as "update" or "fix stuff".
- Avoid overly long or unfocused titles.
- Avoid excessive detail in body bullet points.

## Commit Message Format

```text
<type>(<scope>)!: <title>

<summary>

<footer>
```

- Title: lowercase, no period, max 50 characters
- Scope: optional, feature affected
- Body: optional if commit is straightforward + self-explanatory
- Body: must explain _why_ (not just _what_), can be in prose or bullet points (concise and high-level)
- Body: must disclaim breaking changes if applicable
- Body: should include issue references if applicable

### Allowed Types

| Type     | Description                           |
| -------- | ------------------------------------- |
| feat     | New feature                           |
| fix      | Bug fix                               |
| chore    | Maintenance (tooling, deps)           |
| docs     | Documentation changes                 |
| refactor | Code restructure (no behavior change) |
| test     | Adding or refactoring tests           |
| style    | Code formatting (no logic change)     |
| perf     | Performance improvements              |
