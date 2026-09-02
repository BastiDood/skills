---
name: git-commit
description: Prepare cohesive staged Git commit slices and conventional messages without committing. Use when organizing pending changes into reviewable checkpoints.
compatibility: Requires Git (`git`).
---

# Smart Commit

Turn the current working-tree changes into a clear, reviewable sequence of checkpoints as conventional Git commits. Stage one genuine work slice at a time, then let the user review the handoff and create the commit.

## Workflow

1. Inspect all staged and unstaged changes. Identify the cohesive slices of work that belong in separate commits.
   - Use genuine, independently understandable work slices. Do not split changes into overly granular commits merely because they can be separated.
   - Keep the repository in a consistent state after each proposed commit.
   - Make the sequence tell the story of the completed work.
2. Select the next slice and stage all files and relevant subsets or hunks that belong to it.
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
5. After the user creates the commit, inspect the remaining changes and return to Step 2. Continue until no pending changes remain.

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
