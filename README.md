# skills

Shared [Agent Skills](https://agentskills.io) registry for cross-project reuse. Flat directory structure for unambiguous invocation (e.g., `/svelte-best-practices`).

## Skills

### Workflows

| Skill                                           | Description                                       |
| ----------------------------------------------- | ------------------------------------------------- |
| [`git-commit`](./git-commit/)                   | Conventional commit message generation            |
| [`github-pull-request`](./github-pull-request/) | GitHub PR creation with auto-detected base branch |

### Software Engineering

| Skill                                                                       | Description                                                                           |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| [`vertically-sliced-feature-modules`](./vertically-sliced-feature-modules/) | Feature modules in a [vertically sliced architecture][vertically-sliced-architecture] |

[vertically-sliced-architecture](https://dev.to/somedood/youre-slicing-your-architecture-wrong-4ob9)

### TypeScript

| Skill                               | Description                                          |
| ----------------------------------- | ---------------------------------------------------- |
| [`type-modeling`](./type-modeling/) | TypeScript type modeling and Valibot schema patterns |
| [`better-all`](./better-all/)       | `better-all` library for DAG-based `Promise.all`     |
| [`valibot`](./valibot/)             | Schema validation with Valibot                       |

### React

| Skill                                             | Description                                          |
| ------------------------------------------------- | ---------------------------------------------------- |
| [`react-best-practices`](./react-best-practices/) | Component patterns, state, memoization, side effects |
| [`tanstack-react-query`](./tanstack-react-query/) | TanStack Query with Loader/Inner pattern             |
| [`tanstack-react-table`](./tanstack-react-table/) | TanStack Table with meta field pattern               |

### Svelte

| Skill                                               | Description                                       |
| --------------------------------------------------- | ------------------------------------------------- |
| [`svelte-best-practices`](./svelte-best-practices/) | Runes, state machines, side effects, memoization  |
| [`tanstack-svelte-query`](./tanstack-svelte-query/) | TanStack Svelte Query with runes-based reactivity |
| [`tanstack-svelte-table`](./tanstack-svelte-table/) | TanStack Svelte Table with meta field pattern     |
| [`shadcn-svelte`](./shadcn-svelte/)                 | shadcn-svelte + bits-ui component patterns        |

### Observability

| Skill                               | Description                                    |
| ----------------------------------- | ---------------------------------------------- |
| [`opentelemetry`](./opentelemetry/) | Structured logging, error handling, log levels |

### Rust

| Skill                   | Description                                       |
| ----------------------- | ------------------------------------------------- |
| [`tokio`](./tokio/)     | Async runtime patterns (tasks, signals, channels) |
| [`tracing`](./tracing/) | Structured logging and instrumentation            |

## Usage

Reference skills from your project's `.claude/` or `.agents/` configuration. Each skill follows the [Agent Skills specification](https://agentskills.io/specification): `SKILL.md` + optional `scripts/`, `references/`, `assets/`.

## MCP Dependencies

Some skills conditionally reference MCP servers (used only if available):

| Server                                                         | Purpose                            |
| -------------------------------------------------------------- | ---------------------------------- |
| [Context7](https://context7.com/)                              | Latest library documentation       |
| [DeepWiki](https://docs.devin.ai/work-with-devin/deepwiki-mcp) | Library implementation details     |
| [Svelte](https://svelte.dev/docs/mcp/overview)                 | Svelte documentation and autofixer |
