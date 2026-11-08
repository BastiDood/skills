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

Describe a procedural skill in one "use when" sentence whose gerund phrase states the outcome it produces. The gerund already names both the purpose and the trigger, so a second sentence that restates the outcome is redundant. Leave behavioral guarantees and implementation details (e.g., permissions or side effects) to the body unless they change whether the agent should select the skill.

```yaml
# BAD: implementation details and a guarantee spend the discovery budget without changing selection.
description: Reviews a pull request with your own `gh` identity, posting verified inline comments in one review plus one rolling summary comment edited in place on each re-review. Use when asked to review or re-review a PR. Read-only on the repository.
```

```yaml
# BAD: the trigger sentence restates the outcome sentence.
description: Conducts a GitHub pull request review with verified inline findings and a rolling summary. Use when reviewing a pull request or re-reviewing it after new commits.
```

```yaml
# GOOD: one gerund phrase states the outcome and selects the skill.
description: Use when reviewing a GitHub pull request with verified inline findings and a rolling summary, or re-reviewing it after new commits.
```

Keep two sentences for a documentary skill. Its first sentence is a noun phrase that names the governing subject rather than a trigger, so the `Use when` sentence adds selection intents without restating it.

Name each keyword once per description. Once the first sentence names the library, language, or subject, the trigger lists only the intents and artifacts.

```yaml
# BAD: the library name repeats in the trigger without adding a discriminating intent.
description: Zod v4 conventions for owned wire contracts and explicit validation outcomes. Use when defining or reviewing Zod schemas or parsing untrusted data with Zod.
```

```yaml
# GOOD: the first sentence names the library; the trigger names only intents and artifacts.
description: Zod v4 conventions for owned wire contracts and explicit validation outcomes. Use when defining, debugging, or reviewing schemas or parsing untrusted data.
```

Leave detailed topic and API indexes to `SKILL.md` and routed references. General and specialized skills can apply together. Add exclusions only for genuinely competing workflows.

Do not repeat trigger lists in a `When to Apply` section. The body loads only after discovery has already happened. Use the opening body prose to explain the governing model instead.
