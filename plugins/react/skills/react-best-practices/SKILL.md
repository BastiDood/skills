---
name: react-best-practices
description: Opinionated React conventions for component boundaries, state ownership, derived values, explicit rendering, event work, external-system effects, memoization, context, and native forms. Use when creating or reviewing React components, hooks, conditional UI, form submission flows, state-sharing boundaries, or `useEffect` and `useMemo` usage.
---

# React Best Practices

Read as many linked references as are relevant to the current task before writing or reviewing React component, state, rendering, effect, data-loading, or form code.

## Library Sources

- GitHub repository ID: `react/react`
- Context7 library ID: `/facebook/react`
- DeepWiki repository ID: `react/react`

Use Context7 for current documentation and DeepWiki for implementation details.

## References

- For computed values, use [derived values](./references/derive-values.md).
- For component state ownership, use [state scope](./references/scope-state.md).
- For mutually exclusive modes, use [state machines](./references/state-machines.md).
- For `useMemo`, `useCallback`, or `memo`, use [manual memoization](./references/manual-memoization.md).
- For independent memoized work, use [atomic memoization](./references/atomic-memoization.md).
- For JSX branches, use [explicit rendering](./references/explicit-rendering.md).
- For shared behavior ownership, use [context ownership](./references/context-ownership.md).
- For native-element wrappers, use [native element props](./references/native-element-props.md).
- For content and render callbacks, use [children composition](./references/children-composition.md).
- For non-React systems, use [external-system effects](./references/external-system-effects.md).
- For synchronized copied state, eliminate [derived-state effects](./references/avoid-derived-state-effects.md).
- For user-triggered consequences, use [event work](./references/event-work.md).
- For identity-based state resets, use [remounting](./references/reset-state-by-remounting.md).
- For chained effects, [avoid effect chains](./references/avoid-effect-chains.md).
- For visibility-scoped state, use [conditional mounting](./references/conditional-mounting.md).
- For decoded form input, use [form validation](./references/form-validation.md).
- For form pending, success, and error, use [submission state](./references/submission-state.md).
- For multi-action forms, use [submitter identity](./references/submitter-identity.md).
