---
name: svelte-best-practices
description: Svelte 5 conventions for runes, state ownership, rendering lifecycles, effects, component composition, and SvelteKit forms. Use when creating or reviewing Svelte components, deciding where state or I/O belongs, handling reactive or event-driven work, composing snippets or context, or implementing form actions.
---

# Svelte Best Practices

Treat Svelte reactivity as explicit data flow: own mutable state at the narrowest boundary, derive values from it, and reserve effects for external synchronization so component behavior stays clear and stable.

## Library Sources

- GitHub repository ID: `sveltejs/svelte`
- Context7 library ID: `/sveltejs/svelte`
- DeepWiki repository ID: `sveltejs/svelte`

Use the `svelte` MCP server first for Svelte documentation and autofixing. Use Context7 for additional current documentation and DeepWiki for implementation details.

## References

Read as many linked references as are relevant to the current task before writing or reviewing Svelte code.

- Keep component-owned local state in [runes](./references/runes.md) rather than introducing a store with no clear owner or lifetime.
- Place mutable state in the smallest shared subtree with [state ownership](./references/state-ownership.md), lifting only for sibling consumers.
- Limit [effects](./references/effects.md) to reactive synchronization with an external system, never derivation or event consequences.
- Keep independent computed values separate with [atomic derivations](./references/atomic-derivations.md) so each reacts only to its true inputs.
- Put multi-step pure computation in [complex derivations](./references/complex-derivations.md) instead of obscuring it in reactive side effects.
- Model finite UI flows with [state machines](./references/state-machines.md) so impossible flag combinations cannot occur.
- State rendering conditions explicitly with [explicit rendering](./references/explicit-rendering.md), because truthiness can hide valid zero or empty values.
- Forward the native platform contract through [native element props](./references/native-element-props.md) instead of allowlisting wrapper attributes.
- Compose passive child content with [snippets](./references/snippets.md), adding typed parameters only when the component supplies behavior.
- Create [context](./references/context.md) for one coherent subsystem shared across depths, not as ambient prop forwarding.
- Put reusable reactive abstractions in [rune modules](./references/rune-modules.md) so Svelte compiles their runes and their owner stays explicit.
- Give one-time browser setup an element attachment or mount owner with [one-time setup](./references/one-time-setup.md), rather than making non-reactive work an effect.
- Derive synchronized values through [derived state](./references/derived-state.md), not a second writable copy maintained by `$effect`.
- Perform direct user-action consequences with [event-owned work](./references/event-owned-work.md), rather than translating events into reactive signals.
- Avoid [effect chains](./references/effect-chains.md) by keeping dependent asynchronous stages in one owned operation so reactive intermediates cannot obscure ordering or partial failure.
- When changed domain identity begins a fresh local lifetime, [reset state by remounting](./references/state-reset.md) with `{#key}` rather than synchronizing individual fields through `$effect`.
- When hiding UI must discard its local state, [remove it through conditional mounting](./references/conditional-mounting.md); preserve mounting only by explicit product choice.
- Make a form with already resolved inputs pure through [resolved form props](./references/resolved-form-props.md), keeping its upstream data request outside.
- Prefer SvelteKit's progressive lifecycle in [form action defaults](./references/form-action-defaults.md) when an action exists, rather than competing client submission ownership.
- Return expected invalid input as structured state through [form action validation](./references/form-action-validation.md), not an exception path.
- Call `update()` deliberately after custom enhancement through [enhanced form lifecycle](./references/enhanced-form-lifecycle.md), because a callback replaces SvelteKit defaults.
- Keep representation conversion server-only with [server form decoding](./references/server-form-decoding.md), then validate decoded data at the action boundary.
- Decode SPA form transport values at the client boundary with [client form decoding](./references/client-form-decoding.md) before mutation work receives them.
- Treat pending work as form-wide in [submission state](./references/submission-state.md), with explicit progress and reusable controls after failure.
- Treat absent submitters as valid in [submitter identity](./references/submitter-identity.md) unless distinct actions require button identity.
