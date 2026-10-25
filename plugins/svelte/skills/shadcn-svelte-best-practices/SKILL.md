---
name: shadcn-svelte-best-practices
description: shadcn-svelte and Bits UI conventions for accessible primitive composition and customization. Use when creating, wrapping, restyling, or reviewing those components.
---

# shadcn-svelte Best Practices

Treat shadcn-svelte components as composable accessible primitives: preserve primitive behavior, keep form and dialog ownership clear, and use explicit variants and styling hooks for durable customization.

## Library Sources

| Library       | GitHub Repository ID      | Context7 Library ID        | DeepWiki Repository ID    |
| ------------- | ------------------------- | -------------------------- | ------------------------- |
| shadcn-svelte | `huntabyte/shadcn-svelte` | `/huntabyte/shadcn-svelte` | `huntabyte/shadcn-svelte` |
| Bits UI       | `huntabyte/bits-ui`       | `/huntabyte/bits-ui`       | `huntabyte/bits-ui`       |

Use Context7 for current documentation and DeepWiki for implementation details.

## Effective Strategies for `shadcn-svelte` and Bits UI

Read the references that apply to the current task before writing or reviewing `shadcn-svelte` or Bits UI code.

1. Make primitive wrappers preserve the platform and primitive contracts.
   - [Make Bits UI wrappers drop-in compatible](./references/primitive-wrappers.md) by binding `ref`, merging classes, and forwarding remaining props.
   - [Expose semantic wrapper roles through data slots](./references/data-slots.md) so consumer styling does not depend on volatile implementation classes.
2. Keep state and reusable styling contracts with their appropriate owner.
   - [Give form drafts an intentional lifetime](./references/dialog-form-ownership.md), checking primitive mounting behavior while the shell owns visibility.
   - [Define reusable `tv()` schemas in module scope](./references/variants.md) so their contracts do not reinstantiate per component.
   - [Keep static classes literal and compose conditional classes with `cn`](./references/class-composition.md), preserving required merging and design tokens.
