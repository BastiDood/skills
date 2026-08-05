---
name: github-pull-request
description: Writes a GitHub pull request description using a standardized template. Auto-detects the base branch when diffing the changes. Use when submitting a new PR.
compatibility: Requires Git (`git`) and the GitHub CLI (`gh`).
---

# GitHub Pull Request

Prepare a concise, reviewer-focused pull request from the finalized branch changes. Use the project template and submit the pull request only after the user approves the description.

<workflow-steps>

1. Detect the base branch from the remote:
   ```shell
   BASE=$(gh repo view --json defaultBranchRef --jq '.defaultBranchRef.name')
   ```
2. Compare the current branch with the base branch. Describe the final result, not superseded work from earlier commits.
3. Use the [pull request template](./assets/pull-request-template.md) to generate `.scratchpad/PR.md`. Keep the summary concise and design decisions explicit. In the implementation notes, group related decisions under per-section subheadings that describe their shared goal or motif.
4. Pause here and prompt the user to check the `.scratchpad/PR.md` before proceeding.
5. Once edited and approved by the user, fill in the missing details in the following script and then run it:
   ```bash
   HEAD=$(git rev-parse --abbrev-ref HEAD)
   gh pr create --base "$BASE" --head "$HEAD" --title 'category(scope): concise title' --body-file .scratchpad/PR.md
   ```
6. Delete `.scratchpad/PR.md` once successfully submitted.

</workflow-steps>

<pull-request-title-guidelines>

- Examine the commit messages that make up the pull request for inspiration
- Keep it short and sweet, encapsulating a summary of the implemented feature concisely in imperative form
- Use `category` specifiers like `feat`, `fix`, `docs`, `chore`, `deps`, etc. (consistent with the Conventional Commits message style)
- Determine the `scope` of the changes based on the most affected part of the codebase
- Refer to the GitHub issues provided by the user (if applicable)
  - Mention these issues in natural-flowing prose (with respect to the pull request description)
  - Use keywords like `closes`, `fixes`, and `resolves`

</pull-request-title-guidelines>
