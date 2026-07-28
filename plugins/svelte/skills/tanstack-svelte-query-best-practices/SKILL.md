---
name: tanstack-svelte-query-best-practices
description: TanStack Query conventions for the Svelte adapter, covering loader boundaries, reactive query options, cache identity, and mutation ownership. Use when fetching server data, defining queries or query keys, handling absent request inputs, or implementing mutations in Svelte.
---

# TanStack Svelte Query Best Practices

Read as many linked references as are relevant to the current task before writing or reviewing TanStack Svelte Query code.

## Library Sources

- GitHub repository ID: `TanStack/query`
- Context7 library ID: `/tanstack/query`
- DeepWiki repository ID: `TanStack/query`

Use Context7 for current documentation and DeepWiki for implementation details.

## References

- For query-state rendering, use an [outer loader](./references/outer-loader.md).
- For one server-data lifecycle, use [request ownership](./references/request-ownership.md).
- For loader and inner props, use [loader prop contracts](./references/loader-prop-contracts.md).
- For response-changing inputs, use [cache identity](./references/query-keys.md).
- For captured request values, use [query function inputs](./references/query-function-inputs.md).
- For missing required identifiers, use [absent identifiers](./references/absent-identifiers.md).
- For visibility-sensitive caches, use [actor identity and secrets](./references/actor-identity-and-secrets.md).
- For reactive query configuration, use [reactive query options](./references/reactive-query-options.md).
- For consuming query results, use [reactive result consumption](./references/reactive-result-consumption.md).
- For form submission lifecycles, use [mutation form integration](./references/mutation-form-integration.md).
- For changing mutation inputs, use [mutation variables](./references/mutation-variables.md).
- For mutation callback scope, use [mutation callback ownership](./references/mutation-callback-ownership.md).
- For invocation outcome handling, use [mutation call-site lifecycle](./references/mutation-callsite-lifecycle.md).
- For narrow cache invalidation, use [mutation invalidation](./references/mutation-invalidation.md).
