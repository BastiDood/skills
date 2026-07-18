# Skills by BastiDood

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
| [`type-modeling`](./skills/type-modeling/)                                         | TypeScript type modeling and Valibot schema patterns                                                                      |
| [`vertically-sliced-feature-modules`](./skills/vertically-sliced-feature-modules/) | Feature modules in a [vertically sliced architecture](https://dev.to/somedood/youre-slicing-your-architecture-wrong-4ob9) |

## TypeScript

| Skill                                | Description                                      |
| ------------------------------------ | ------------------------------------------------ |
| [`better-all`](./skills/better-all/) | `better-all` library for DAG-based `Promise.all` |
| [`valibot`](./skills/valibot/)       | Schema validation with Valibot                   |

## React

| Skill                                                    | Description                                          |
| -------------------------------------------------------- | ---------------------------------------------------- |
| [`react-best-practices`](./skills/react-best-practices/) | Component patterns, state, memoization, side effects |
| [`tanstack-react-query`](./skills/tanstack-react-query/) | TanStack Query with Loader/Inner pattern             |
| [`tanstack-react-table`](./skills/tanstack-react-table/) | TanStack Table with meta field pattern               |

## Svelte

| Skill                                                      | Description                                       |
| ---------------------------------------------------------- | ------------------------------------------------- |
| [`shadcn-svelte`](./skills/shadcn-svelte/)                 | shadcn-svelte + bits-ui component patterns        |
| [`svelte-best-practices`](./skills/svelte-best-practices/) | Runes, state machines, side effects, memoization  |
| [`tanstack-svelte-query`](./skills/tanstack-svelte-query/) | TanStack Svelte Query with runes-based reactivity |
| [`tanstack-svelte-table`](./skills/tanstack-svelte-table/) | TanStack Svelte Table with meta field pattern     |

## Observability

| Skill                                        | Description                                    |
| -------------------------------------------- | ---------------------------------------------- |
| [`open-telemetry`](./skills/open-telemetry/) | Structured logging, error handling, log levels |

## Usage

Reference skills from your project's `.claude/` or `.agents/` configuration. Each skill follows the [Agent Skills specification](https://agentskills.io/specification): `SKILL.md` + optional `scripts/`, `references/`, `assets/`.

## MCP Dependencies

Some skills conditionally reference MCP servers (used only if available):

| Server                                                         | Purpose                            |
| -------------------------------------------------------------- | ---------------------------------- |
| [Context7](https://context7.com/)                              | Latest library documentation       |
| [DeepWiki](https://docs.devin.ai/work-with-devin/deepwiki-mcp) | Library implementation details     |
| [Svelte](https://svelte.dev/docs/mcp/overview)                 | Svelte documentation and autofixer |
