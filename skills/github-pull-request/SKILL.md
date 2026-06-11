---
name: github-pull-request
description: Create a GitHub pull request with auto-detected base branch and structured template. Use when opening a new PR.
compatibility: Requires Git (`git`) and the GitHub CLI (`gh`).
---

Summarize the latest changes in this branch to create a pull request on GitHub.

<workflow-steps>

1. Detect the base branch from the remote:
   ```shell
   BASE=$(gh repo view --json defaultBranchRef --jq '.defaultBranchRef.name')
   ```
2. Compare the current branch against the base branch to see what changes need to be described in the pull request. Focus on the finalized implementation details. Since pull requests tend to have work-in-progress commits at the beginning, be extra mindful on whether these are still relevant in the finalized snapshot.
3. Use the [pull request template](assets/pull-request-template.md) to generate a `.scratchpad/PR.md`. Fill in the placeholder sections and text.
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
