# Skills by BastiDood

Opinionated software engineering skills distributed as installable plugins for Codex, Claude Code, and Cursor. Each section below is a plugin containing related skills.

<details>
<summary>Codex</summary>

```sh
codex plugin marketplace add BastiDood/skills

codex plugin add workflows@bastidood
codex plugin add architecture@bastidood
codex plugin add meta@bastidood
codex plugin add typescript@bastidood
codex plugin add python@bastidood
codex plugin add react@bastidood
codex plugin add svelte@bastidood
codex plugin add observability@bastidood
```

</details>

<details>
<summary>Claude Code</summary>

```sh
claude plugin marketplace add BastiDood/skills

claude plugin install workflows@bastidood
claude plugin install architecture@bastidood
claude plugin install meta@bastidood
claude plugin install typescript@bastidood
claude plugin install python@bastidood
claude plugin install react@bastidood
claude plugin install svelte@bastidood
claude plugin install observability@bastidood
```

</details>

<details>
<summary>Cursor</summary>

For Cursor Teams and Enterprise, import `https://github.com/BastiDood/skills` from **Dashboard -> Plugins -> Add Marketplace**, then install the desired plugins from **Customize**.

</details>

Some skills conditionally reference MCP servers (used only if available):

| Server                                                         | Purpose                            |
| -------------------------------------------------------------- | ---------------------------------- |
| [Context7](https://context7.com/)                              | Latest library documentation       |
| [DeepWiki](https://docs.devin.ai/work-with-devin/deepwiki-mcp) | Library implementation details     |
| [Svelte](https://svelte.dev/docs/mcp/overview)                 | Svelte documentation and autofixer |

## Workflows

| Skill                                                                                      | Description                                                 |
| ------------------------------------------------------------------------------------------ | ----------------------------------------------------------- |
| [`adversarial-code-review`](./plugins/workflows/skills/adversarial-code-review/)           | Adversarial code review against the spec and implementation |
| [`git-commit`](./plugins/workflows/skills/git-commit/)                                     | Conventional commit message generation                      |
| [`github-pull-request`](./plugins/workflows/skills/github-pull-request/)                   | GitHub PR creation with auto-detected base branch           |
| [`github-pull-request-comments`](./plugins/workflows/skills/github-pull-request-comments/) | Verifies, triages, and rebuts GitHub pull request comments  |

## Architecture

| Skill                                                                                                   | Description                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`code-style`](./plugins/architecture/skills/code-style/)                                               | Explicit, invariant-driven, language-agnostic code rules                                                                                                                                          |
| [`testing-guidelines`](./plugins/architecture/skills/testing-guidelines/)                               | Test project-owned behavior without retesting dependencies                                                                                                                                        |
| [`vertically-sliced-feature-modules`](./plugins/architecture/skills/vertically-sliced-feature-modules/) | Feature-owned UI, workflows, nested capabilities, shared boundaries, and package admission in a [vertical-slice architecture](https://dev.to/somedood/youre-slicing-your-architecture-wrong-4ob9) |

## Meta

| Skill                                                                               | Description                                                                    |
| ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| [`agent-skills-best-practices`](./plugins/meta/skills/agent-skills-best-practices/) | Opinionated skill naming, context design, routing, examples, and assets        |
| [`plugin-best-practices`](./plugins/meta/skills/plugin-best-practices/)             | Focused plugin boundaries, encapsulated public surfaces, and minimal manifests |

## TypeScript

| Skill                                                                                 | Description                                         |
| ------------------------------------------------------------------------------------- | --------------------------------------------------- |
| [`better-all-best-practices`](./plugins/typescript/skills/better-all-best-practices/) | `better-all` library for DAG-based `Promise.all`    |
| [`typescript-best-practices`](./plugins/typescript/skills/typescript-best-practices/) | Inference, exhaustiveness, and strict index access  |
| [`valibot-best-practices`](./plugins/typescript/skills/valibot-best-practices/)       | Nullability, discriminated unions, and parse policy |
| [`zod-best-practices`](./plugins/typescript/skills/zod-best-practices/)               | Top-level formats, nullability, and parse policy    |

## Python

| Skill                                                                     | Description                                                    |
| ------------------------------------------------------------------------- | -------------------------------------------------------------- |
| [`python-best-practices`](./plugins/python/skills/python-best-practices/) | Typing, imports, packaging, validation, and resource lifetimes |

## React

| Skill                                                                                                | Description                                           |
| ---------------------------------------------------------------------------------------------------- | ----------------------------------------------------- |
| [`react-best-practices`](./plugins/react/skills/react-best-practices/)                               | Component patterns, state, memoization, side effects  |
| [`tanstack-react-query-best-practices`](./plugins/react/skills/tanstack-react-query-best-practices/) | Loader/Inner pattern, query hooks, cache invalidation |
| [`tanstack-react-table-best-practices`](./plugins/react/skills/tanstack-react-table-best-practices/) | Hoisted columns with the meta field pattern           |

## Svelte

| Skill                                                                                                   | Description                                          |
| ------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| [`shadcn-svelte-best-practices`](./plugins/svelte/skills/shadcn-svelte-best-practices/)                 | Wrapping Bits UI primitives, variants, styling hooks |
| [`svelte-best-practices`](./plugins/svelte/skills/svelte-best-practices/)                               | Runes, state machines, side effects, memoization     |
| [`tanstack-svelte-query-best-practices`](./plugins/svelte/skills/tanstack-svelte-query-best-practices/) | Outer-Loader pattern, reactive queries, mutations    |
| [`tanstack-svelte-table-best-practices`](./plugins/svelte/skills/tanstack-svelte-table-best-practices/) | Hoisted columns, meta field pattern, reactive data   |

## Observability

| Skill                                                                                            | Description                                        |
| ------------------------------------------------------------------------------------------------ | -------------------------------------------------- |
| [`open-telemetry-best-practices`](./plugins/observability/skills/open-telemetry-best-practices/) | Log levels, attribute naming, error classification |
