---
name: tanstack-react-query-best-practices
description: 'Opinionated TanStack Query conventions for React server state: resolved-view boundaries, query ownership, cache identity, disabled inputs, actor-scoped data, mutations, form submissions, and invalidation. Use when creating or reviewing `useQuery` or `useMutation` hooks, query keys, fetch functions, mutation callbacks, loading/error views, or cache updates in React.'
---

# TanStack React Query Best Practices

Read as many linked references as are relevant to the current task before writing or reviewing TanStack Query code.

## Library Sources

- GitHub repository ID: `TanStack/query`
- Context7 library ID: `/tanstack/query`
- DeepWiki repository ID: `TanStack/query`

Use Context7 for current documentation and DeepWiki for implementation details.

## References

- For query view boundaries, use [Loader and Inner](./references/loader-and-inner.md).
- For resolved-view inputs, use [loader prop contracts](./references/loader-prop-contracts.md).
- For server-state ownership, use [request ownership](./references/request-ownership.md).
- For response-varying inputs, use [cache identity](./references/cache-identity.md).
- For query request values, use [query function inputs](./references/query-function-inputs.md).
- For unavailable identifiers, use [absent identifiers](./references/absent-identifiers.md).
- For actor-scoped cached data, use [actor identity and secrets](./references/actor-identity-and-secrets.md).
- For selected query fields, use [result destructuring](./references/result-destructuring.md).
- For action-specific mutation input, use [mutation variables](./references/mutation-variables.md).
- For browser form mutations, use [mutation form integration](./references/mutation-form-integration.md).
- For mutation outcomes, use [mutation callback lifecycle](./references/mutation-callback-lifecycle.md).
- For post-write refreshes, use [mutation invalidation](./references/mutation-invalidation.md).
