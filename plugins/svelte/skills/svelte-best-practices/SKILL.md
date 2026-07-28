---
name: svelte-best-practices
description: Svelte 5 conventions for runes, state ownership, rendering lifecycles, effects, component composition, and SvelteKit forms. Use when creating or reviewing Svelte components, deciding where state or I/O belongs, handling reactive or event-driven work, composing snippets or context, or implementing form actions.
---

# Svelte Best Practices

Read as many linked references as are relevant to the current task before writing or reviewing Svelte code.

## Library Sources

- GitHub repository ID: `sveltejs/svelte`
- Context7 library ID: `/sveltejs/svelte`
- DeepWiki repository ID: `sveltejs/svelte`

Use the `svelte` MCP server first for Svelte documentation and autofixing. Use Context7 for additional current documentation and DeepWiki for implementation details.

## References

- For local reactive state, use [runes](./references/runes.md).
- For independent calculations, use [atomic derivations](./references/atomic-derivations.md).
- For multi-step pure derivations, use [complex derivations](./references/complex-derivations.md).
- For mutually exclusive UI states, use [state machines](./references/state-machines.md).
- For mutable-state placement, use [state ownership](./references/state-ownership.md).
- For non-truthy display rules, use [explicit rendering](./references/explicit-rendering.md).
- For native-element wrappers, use [native element props](./references/native-element-props.md).
- For child composition contracts, use [snippets](./references/snippets.md).
- For shared-state scope, use [context](./references/context.md).
- For runes outside components, use [rune modules](./references/rune-modules.md).
- For external-system synchronization, constrain [effects](./references/effects.md).
- For browser setup and teardown, use [one-time setup](./references/one-time-setup.md).
- For synchronized local state, prefer [derived state](./references/derived-state.md).
- For user-action consequences, use [event-owned work](./references/event-owned-work.md).
- For ordered asynchronous work, avoid [effect chains](./references/effect-chains.md).
- For identity-driven local resets, use [state reset](./references/state-reset.md).
- For visibility and state retention, use [conditional mounting](./references/conditional-mounting.md).
- For forms with resolved data, use [resolved form props](./references/resolved-form-props.md).
- For SvelteKit or SPA submission, use [form action defaults](./references/form-action-defaults.md).
- For expected invalid input, use [form action validation](./references/form-action-validation.md).
- For customized `use:enhance`, use [enhanced form lifecycle](./references/enhanced-form-lifecycle.md).
- For server-action value decoding, use [server form decoding](./references/server-form-decoding.md).
- For SPA mutation value decoding, use [client form decoding](./references/client-form-decoding.md).
- For form-wide pending progress, use [submission state](./references/submission-state.md).
- For submitterless submission, use [submitter identity](./references/submitter-identity.md).
