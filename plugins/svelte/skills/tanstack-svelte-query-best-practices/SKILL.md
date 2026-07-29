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

## References

Read as many linked references as are relevant to the current task before writing or reviewing TanStack Svelte Query code.

- Let TanStack Query own one server-data lifecycle through [request ownership](./references/request-ownership.md), instead of duplicating requests in effects or handlers.
- Include every response-changing input in [cache identity](./references/query-keys.md) so filters, tenant, locale, and pagination cannot share stale results.
- Resolve pending, error, empty, and success states in an [outer loader](./references/outer-loader.md) before mounting stateful presentation.
- Publish identity at the loader boundary through [loader prop contracts](./references/loader-prop-contracts.md) and give resolved components only the data they need.
- Keep request construction visibly coupled to cache identity with [query function inputs](./references/query-function-inputs.md), including every captured response-changing value.
- Preserve missing required IDs through [absent identifiers](./references/absent-identifiers.md) instead of issuing a fabricated empty-record request.
- Partition visibility-sensitive cache entries by non-secret actor identity with [actor identity and secrets](./references/actor-identity-and-secrets.md).
- Give `createQuery` an accessor in [reactive query options](./references/reactive-query-options.md) so changing inputs update the query instead of freezing options.
- Read query fields directly through [reactive result consumption](./references/reactive-result-consumption.md), because destructuring snapshots their values.
- Choose one submission lifecycle with [mutation form integration](./references/mutation-form-integration.md); a form cannot simultaneously own SvelteKit enhancement and a client mutation.
- Pass changed operation input via [mutation variables](./references/mutation-variables.md), keeping definition configuration stable.
- Put shared cache policy in the definition and component-specific UI work at the call site with [mutation callback ownership](./references/mutation-callback-ownership.md).
- Keep each invocation's success, failure, and settlement behavior in [mutation call-site lifecycle](./references/mutation-callsite-lifecycle.md), not an effect that loses invocation identity.
- Invalidate the narrow record or list family made stale through [mutation invalidation](./references/mutation-invalidation.md).
