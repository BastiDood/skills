# Context and Disclosure

Inline in `SKILL.md` the minimum context that every invocation needs to understand and apply the skill. A reference creates no progressive disclosure when the entry point always requires the agent to read it. Start with an orientation that states the governing model and intended outcome, short enough to prime the agent without becoming a second reference.

Move content behind a reference only when it is conditional, detail-oriented, or specific to one branch of the task. Do not require every reference by default. Load as much as the current task needs.

<progressive_disclosure_examples>

```markdown
<!-- BAD: the entry point opens with its references and never states the governing opinion. -->

# API Best Practices

## Effective Strategies

1. Validate external data before it becomes a domain value.
   - [Parse untrusted input at the controlled boundary.](./references/input-validation.md)
2. Preserve information needed for caller-owned policy.
   - [Expose pagination without choosing when traversal stops.](./references/pagination.md)
```

```markdown
<!-- GOOD: the entry point explains the governing opinion first. -->

# API Best Practices

Design APIs around stable domain contracts rather than transport convenience. Keep caller policy visible and preserve information across boundaries.

## Effective Strategies

1. Validate external data before it becomes a domain value.
   - [Parse untrusted input at the controlled boundary.](./references/input-validation.md)
2. Preserve information needed for caller-owned policy.
   - [Expose pagination without choosing when traversal stops.](./references/pagination.md)
```

</progressive_disclosure_examples>
