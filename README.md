# Skills by BastiDood

Some skills conditionally reference MCP servers (used only if available):

| Server                                                         | Purpose                            |
| -------------------------------------------------------------- | ---------------------------------- |
| [Context7](https://context7.com/)                              | Latest library documentation       |
| [DeepWiki](https://docs.devin.ai/work-with-devin/deepwiki-mcp) | Library implementation details     |
| [Svelte](https://svelte.dev/docs/mcp/overview)                 | Svelte documentation and autofixer |

## Workflows

| Skill                                                                    | Description                                                 |
| ------------------------------------------------------------------------ | ----------------------------------------------------------- |
| [`adversarial-code-review`](./skills/adversarial-code-review/)           | Adversarial code review against the spec and implementation |
| [`git-commit`](./skills/git-commit/)                                     | Conventional commit message generation                      |
| [`github-pull-request`](./skills/github-pull-request/)                   | GitHub PR creation with auto-detected base branch           |
| [`github-pull-request-comments`](./skills/github-pull-request-comments/) | Verifies, triages, and rebuts GitHub pull request comments  |

## Architecture

| Skill                                                                              | Description                                                                                                               |
| ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| [`vertically-sliced-feature-modules`](./skills/vertically-sliced-feature-modules/) | Feature modules in a [vertically sliced architecture](https://dev.to/somedood/youre-slicing-your-architecture-wrong-4ob9) |

## TypeScript

| Skill                                                              | Description                                         |
| ------------------------------------------------------------------ | --------------------------------------------------- |
| [`better-all-best-practices`](./skills/better-all-best-practices/) | `better-all` library for DAG-based `Promise.all`    |
| [`typescript-best-practices`](./skills/typescript-best-practices/) | Inference, exhaustiveness, and strict index access  |
| [`valibot-best-practices`](./skills/valibot-best-practices/)       | Nullability, discriminated unions, and parse policy |
| [`zod-best-practices`](./skills/zod-best-practices/)               | Top-level formats, nullability, and parse policy    |

## React

| Skill                                                                                  | Description                                           |
| -------------------------------------------------------------------------------------- | ----------------------------------------------------- |
| [`react-best-practices`](./skills/react-best-practices/)                               | Component patterns, state, memoization, side effects  |
| [`tanstack-react-query-best-practices`](./skills/tanstack-react-query-best-practices/) | Loader/Inner pattern, query hooks, cache invalidation |
| [`tanstack-react-table-best-practices`](./skills/tanstack-react-table-best-practices/) | Hoisted columns with the meta field pattern           |

## Svelte

| Skill                                                                                    | Description                                          |
| ---------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| [`shadcn-svelte-best-practices`](./skills/shadcn-svelte-best-practices/)                 | Wrapping Bits UI primitives, variants, styling hooks |
| [`svelte-best-practices`](./skills/svelte-best-practices/)                               | Runes, state machines, side effects, memoization     |
| [`tanstack-svelte-query-best-practices`](./skills/tanstack-svelte-query-best-practices/) | Outer-Loader pattern, reactive queries, mutations    |
| [`tanstack-svelte-table-best-practices`](./skills/tanstack-svelte-table-best-practices/) | Hoisted columns, meta field pattern, reactive data   |

## Observability

| Skill                                                                      | Description                                        |
| -------------------------------------------------------------------------- | -------------------------------------------------- |
| [`open-telemetry-best-practices`](./skills/open-telemetry-best-practices/) | Log levels, attribute naming, error classification |
