---
name: svelte-best-practices
description: Svelte 5 and SvelteKit conventions for explicit reactive ownership and component lifecycles. Use when creating or reviewing Svelte components, runes, context, or form actions.
---

# Svelte Best Practices

Treat Svelte reactivity as explicit data flow: own mutable state at the narrowest boundary, derive values from it, and reserve effects for external synchronization so component behavior stays clear and stable.

## Library Sources

- GitHub repository ID: `sveltejs/svelte`
- Context7 library ID: `/sveltejs/svelte`
- DeepWiki repository ID: `sveltejs/svelte`

Use the `svelte` MCP server first for Svelte documentation and autofixing. Use Context7 for additional current documentation and DeepWiki for implementation details.

## References

Read the references that apply to the current task before writing or reviewing Svelte code.

1. Own reactive state narrowly and derive all values that its inputs prove.
   - [Keep component-owned local state in runes](./references/runes.md) rather than introducing a store with no clear owner or lifetime.
   - [Place mutable state in the smallest shared subtree](./references/state-ownership.md), lifting only for sibling consumers.
   - [Cache pure reactive computations worse than O(1) in independent derivations](./references/atomic-derivations.md), including scalar and O(log n) work.
   - [Put multi-step pure computation in complex derivations](./references/complex-derivations.md) instead of obscuring it in reactive side effects.
   - [Model finite UI flows with state machines](./references/state-machines.md) so impossible flag combinations cannot occur.
   - [Put cohesive reactive abstractions in rune modules](./references/rune-modules.md) so Svelte compiles their runes, even with one consumer.
   - [Derive synchronized values through derived state](./references/derived-state.md), not a second writable copy maintained by `$effect`.
   - [Reset changed domain identity with `{#key}`](./references/state-reset.md) rather than synchronizing individual fields through `$effect`.
   - [Remove hidden UI through conditional mounting](./references/conditional-mounting.md) when it must discard local state; preserve mounting only by explicit product choice.
2. Render and compose UI through explicit, owned contracts.
   - [State rendering conditions explicitly](./references/explicit-rendering.md), because truthiness can hide valid zero or empty values.
   - [Forward the native platform contract through native element props](./references/native-element-props.md) instead of allowlisting wrapper attributes.
   - [Compose passive child content with snippets](./references/snippets.md), adding typed parameters only when the component supplies behavior.
   - [Create context for one coherent subsystem shared across depths](./references/context.md), not as ambient prop forwarding.
3. Restrict effects and event work to their actual external or action owners.
   - [Limit effects to reactive external-system synchronization](./references/effects.md), never derivation or event consequences.
   - [Give one-time browser setup an element attachment or mount owner](./references/one-time-setup.md) rather than making non-reactive work an effect.
   - [Perform direct user-action consequences with event-owned work](./references/event-owned-work.md) rather than translating events into reactive signals.
   - [Keep dependent asynchronous stages in one owned operation](./references/effect-chains.md) to avoid effect chains that obscure ordering or partial failure.
4. Make form boundaries explicit and give each lifecycle one owner.
   - [Make a form with resolved inputs pure through resolved form props](./references/resolved-form-props.md), keeping its upstream data request outside.
   - [Prefer SvelteKit's progressive lifecycle through form action defaults](./references/form-action-defaults.md) when an action exists rather than competing client submission ownership.
   - [Return expected invalid input as structured state](./references/form-action-validation.md), not an exception path.
   - [Call `update()` deliberately after custom enhancement](./references/enhanced-form-lifecycle.md), because a callback replaces SvelteKit defaults.
   - [Keep representation conversion server-only](./references/server-form-decoding.md), then validate decoded data at the action boundary.
   - [Decode SPA form transport values at the client boundary](./references/client-form-decoding.md) before mutation work receives them.
   - [Treat pending work as form-wide submission state](./references/submission-state.md), with explicit progress and reusable controls after failure.
   - [Treat absent submitters as valid](./references/submitter-identity.md) unless distinct actions require button identity.
