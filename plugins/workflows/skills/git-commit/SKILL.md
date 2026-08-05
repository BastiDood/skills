---
name: git-commit
description: Organize pending changes into cohesive work slices, stage each slice, and propose conventional Git commit titles for the user to review and commit. Use when preparing incremental checkpoints as reviewable staged Git commits.
compatibility: Requires Git (`git`).
---

# Smart Commit

Turn the current working-tree changes into a clear, reviewable sequence of checkpoints as conventional Git commits. Stage one genuine work slice at a time, then let the user review the proposed title and create the commit.

1. Inspect all staged and unstaged changes. Identify the cohesive slices of work that belong in separate commits.
   - Use genuine, independently understandable work slices. Do not split changes into overly granular commits merely because they can be separated.
   - Keep the repository in a consistent state after each proposed commit.
   - Make the sequence tell the story of the completed work.
2. Select the next slice and stage all files and relevant subsets or hunks that belong to it.
   - Include the complete slice, including its tests and documentation when applicable.
   - Use partial-file staging when a file contains changes for more than one slice.
   - Review the staged diff and confirm that it contains the intended slice and no unrelated changes.
3. Propose a conventional commit title based only on the staged diff. Ask the user to review the title and create the commit.
   - Never run `git commit`. The user must create every commit unless explicitly delegated to you.
   - Stop and wait for the user after proposing the title.
4. After the user creates the commit, inspect the remaining changes and return to Step 2. Continue until no pending changes remain.

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

## Examples

- [Full commit message with all the optional details provided](./references/example.txt)
