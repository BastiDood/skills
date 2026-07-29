---
name: shadcn-svelte-best-practices
description: Opinionated Shadcn Svelte + Bits UI conventions for primitive wrappers, dialog and form ownership, variants, class composition, and styling hooks. Use when adding, wrapping, restyling, or reviewing Shadcn Svelte or Bits UI components in Svelte.
---

# shadcn-svelte Best Practices

Treat shadcn-svelte components as composable accessible primitives: preserve primitive behavior, keep form and dialog ownership clear, and use explicit variants and styling hooks for durable customization.

## Library Sources

| Library       | GitHub Repository ID      | Context7 Library ID        | DeepWiki Repository ID    |
| ------------- | ------------------------- | -------------------------- | ------------------------- |
| shadcn-svelte | `huntabyte/shadcn-svelte` | `/huntabyte/shadcn-svelte` | `huntabyte/shadcn-svelte` |
| Bits UI       | `huntabyte/bits-ui`       | `/huntabyte/bits-ui`       | `huntabyte/bits-ui`       |

Use Context7 for current documentation and DeepWiki for implementation details.

## References

Read as many linked references as are relevant to the current task before writing or reviewing `shadcn-svelte` or Bits UI code.

- Make Bits UI wrappers drop-in compatible through [primitive wrappers](./references/primitive-wrappers.md) that bind `ref`, merge classes, and forward remaining props.
- Keep mutable form state inside the mounted form with [dialog form ownership](./references/dialog-form-ownership.md), leaving the dialog shell responsible only for visibility.
- Define reusable `tv()` schemas in module scope with [variants](./references/variants.md) so their contracts do not reinstantiate per component.
- Express conditional utility classes with [class composition](./references/class-composition.md), using object syntax for independent classes and ternaries for alternatives.
- Expose semantic wrapper roles through [data slots](./references/data-slots.md) so consumer styling does not depend on volatile implementation classes.
