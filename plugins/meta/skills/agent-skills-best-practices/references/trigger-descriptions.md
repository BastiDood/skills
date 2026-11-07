# Trigger Descriptions

Treat the frontmatter description as the discovery contract. First state the skill's distinctive capability or governing opinion. Then name only the smallest set of discriminating user intents, artifacts, or decisions that make it relevant.

```yaml
# BAD: the description does not give the agent a usable trigger.
description: Helps with agent skills.
```

```yaml
# GOOD: a documentary skill states its governing subject and discriminating decisions.
description: PostgreSQL query and schema-design conventions. Use when designing tables, reviewing indexes, or diagnosing query plans.
```

Lead a procedural skill's description with the outcome it produces. Leave behavioral guarantees and implementation details (e.g., permissions or side effects) to the body unless they change whether the agent should select the skill.

```yaml
# BAD: implementation details and a guarantee spend the discovery budget without changing selection.
description: Reviews a pull request with your own `gh` identity, posting verified inline comments in one review plus one rolling summary comment edited in place on each re-review. Use when asked to review or re-review a PR. Read-only on the repository.
```

```yaml
# GOOD: the outcome comes first, then the intents that select it.
description: Conducts a GitHub pull request review with verified inline findings and a rolling summary. Use when reviewing a pull request or re-reviewing it after new commits.
```

Leave detailed topic and API indexes to `SKILL.md` and routed references. General and specialized skills can apply together. Add exclusions only for genuinely competing workflows.

Do not repeat trigger lists in a `When to Apply` section. The body loads only after discovery has already happened. Use the opening body prose to explain the governing model instead.
