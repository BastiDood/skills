---
name: git-commit
description: Generate commit messages following conventional commits and commit staged changes. Use when creating commits or when user invokes /commit.
compatibility: Requires Git (`git`).
---

# Smart Commit

1. Explore both the staged/unstaged changes in the current working tree.
2. Realize a mind map of the feature being implemented.
3. Logically group the changed files/hunks into (possibly several) self-contained atomic commits. Do not hesitate to granularly stage hunks for proper commit isolation.
4. Strive to keep the codebase in a consistent state after each commit.
5. Optimize for readability and reviewability of the commit history. The commits should tell the story of how the feature was implemented.

## General Guidelines

- Single commits are permitted if sufficiently straightforward
- Only generate the message for staged files/changes
- Don't add any files using `git add` - user decides what to add
- Do NOT add any ads or footers
- Avoid vague titles: "update", "fix stuff"
- Avoid overly long or unfocused titles
- Avoid excessive detail in bullet points

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
