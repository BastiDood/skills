---
name: tanstack-react-query-best-practices
description: TanStack Query conventions for cache-backed React server state. Use when creating, debugging, or reviewing queries, mutations, query keys, or cache updates.
---

# TanStack React Query Best Practices

Treat server data as cache-backed asynchronous state with an explicit owner, identity, and view boundary; the outcome is reliable loading and error handling plus focused updates after writes.

## Library Sources

- GitHub repository ID: `TanStack/query`
- Context7 library ID: `/tanstack/query`
- DeepWiki repository ID: `TanStack/query`

Use Context7 for current documentation and DeepWiki for implementation details.

## References

Read the references that apply to the current task before writing or reviewing TanStack Query code.

1. Let TanStack Query own server-request lifecycle and cache identity.
   - [Define each entity's queries as `queryOptions()` factories](./references/query-factories.md) that own keys, query functions, and key prefixes, instead of custom hooks that only wrap `useQuery`.
   - [Let TanStack Query own request lifecycle](./references/request-ownership.md) rather than duplicating it with effects, local state, or a component-level `QueryClient`.
   - [Overlay explicit local selection choices on authoritative query records](./references/selection-overlays.md) when the feature combines server defaults with user overrides.
   - [Put every response-changing request input in cache identity](./references/cache-identity.md) so distinct results never reuse one entry.
   - [Read keyed request inputs from the query function's context](./references/query-function-inputs.md), and keep every response-changing capture in the key.
   - [Preserve absent identifiers and disable the request](./references/absent-identifiers.md) instead of defaulting any value that flows into a key.
   - [Partition visibility-sensitive cached data by actor identity](./references/actor-identity-and-secrets.md) without exposing credentials in keys.
2. Resolve query status before presentation consumes data.
   - [Resolve query status at a Loader and Inner boundary](./references/loader-and-inner.md), keeping presentation free of optional data and transport state.
   - [Publish query identity through loader prop contracts](./references/loader-prop-contracts.md) while resolved components receive non-null data.
   - [Destructure every consumed query-result field](./references/result-destructuring.md) because the complete result object changes identity on every render.
3. Keep mutation data, callbacks, and cache updates scoped to each operation.
   - [Destructure every consumed mutation-result field](./references/mutation-result-destructuring.md) because the complete `useMutation` result is not referentially stable.
   - [Pass changing operation input through mutation variables](./references/mutation-variables.md), not a mutation hook's construction closure.
   - [Decode and parse `FormData` in `onSubmit` before passing domain input to the mutation](./references/mutation-form-integration.md), independently of live input state.
   - [Separate mutation callback lifecycles](./references/mutation-callback-lifecycle.md) by putting every-invocation policy in the definition and mounted UI consequences at the call site, and reserve `mutateAsync` for concurrent invocations awaited by a `Promise.*` combinator.
   - [Invalidate the narrow stale entry identified by successful variables](./references/mutation-invalidation.md), and await the invalidation so the mutation stays pending until the refetch completes.
