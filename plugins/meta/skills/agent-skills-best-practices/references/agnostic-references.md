# Agnostic References

Write guidance that works without the source project's names, paths, private artifacts, or history. Include the context a fresh installation needs, or link to an authoritative source.

Preserve the rule's conditions, failure modes, and checks. Keep language- and framework-specific examples in the relevant skill.

Separate shared principles from framework-specific implementations. For example, cache a derivation with `useMemo` in React and `$derived` in Svelte. Keep each example consistent with its framework's syntax and lifecycle.

Look up database IDs, channel IDs, view URLs, and similar infrastructure identifiers at runtime when the workflow can query them. Keep an identifier in the prose only when no lookup exists, such as the entry-point URL of a registry. A hard-coded identifier breaks silently when the resource moves.
