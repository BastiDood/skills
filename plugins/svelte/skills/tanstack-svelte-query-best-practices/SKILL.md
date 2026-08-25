---
name: tanstack-svelte-query-best-practices
description: TanStack Query conventions for the Svelte adapter, covering loader boundaries, reactive query options, cache identity, and mutation ownership. Use when fetching server data, defining queries or query keys, handling absent request inputs, or implementing mutations in Svelte.
---

# TanStack Svelte Query Best Practices

Treat server data as reactive cache state with explicit request ownership and cache identity; this produces clear query-state views and mutations that refresh only the data they affect.

## Library Sources

- GitHub repository ID: `TanStack/query`
- Context7 library ID: `/tanstack/query`
- DeepWiki repository ID: `TanStack/query`

Use Context7 for current documentation and DeepWiki for implementation details.

## Effective Strategies for TanStack Svelte Query

Read the references that apply to the current task before writing or reviewing TanStack Svelte Query code.

1. Let TanStack Query own server-data lifecycle and reactive cache identity.
   - [Let TanStack Query own one server-data lifecycle](./references/request-ownership.md) instead of duplicating requests in effects or handlers.
   - [Include every response-changing input in cache identity](./references/query-keys.md) so filters, tenant, locale, and pagination cannot share stale results.
   - [Keep request construction visibly coupled to cache identity](./references/query-function-inputs.md), including every captured response-changing value.
   - [Preserve missing required IDs](./references/absent-identifiers.md) instead of issuing a fabricated empty-record request.
   - [Partition visibility-sensitive cache entries by non-secret actor identity](./references/actor-identity-and-secrets.md).
   - [Give `createQuery` an accessor for reactive query options](./references/reactive-query-options.md) so changing inputs update the query instead of freezing options.
2. Resolve query status before stateful presentation consumes data.
   - [Resolve pending, error, empty, and success states in an outer loader](./references/outer-loader.md) before mounting stateful presentation.
   - [Publish identity at the loader boundary through loader prop contracts](./references/loader-prop-contracts.md) and give resolved components only the data they need.
   - [Read query fields directly through reactive result consumption](./references/reactive-result-consumption.md), because destructuring snapshots their values.
3. Keep mutation lifecycle and invalidation scoped to the submitting operation.
   - [Choose one submission lifecycle](./references/mutation-form-integration.md); a form cannot simultaneously own SvelteKit enhancement and a client mutation.
   - [Pass changed operation input via mutation variables](./references/mutation-variables.md), keeping definition configuration stable.
   - [Put shared cache policy in the definition and component-specific UI work at the call site](./references/mutation-callback-ownership.md).
   - [Keep each invocation's success, failure, and settlement behavior at the mutation call site](./references/mutation-callsite-lifecycle.md), not an effect that loses invocation identity.
   - [Invalidate the narrow stale record or list family](./references/mutation-invalidation.md).
