# Reference Routing

Link every reference exactly once from `SKILL.md` with an explicit `./` relative path, through an inline citation or a compact resource index such as "References". Name index sections for the decisions they organize. Keep references one hop from the entry point without linking from one reference to another.

Put the governing hook before progressive disclosure so the agent learns the triggering situation, opinion, or failure mode without opening the reference. Hyperlink the phrase that names the relevant decision.

Use the smallest structure that expresses the guidance clearly. A router can be an inline citation, one list item, or an indented link beneath a parent strategy shared by several references. Hierarchical lists are appropriate when the parent states the shared opinion and each child names a distinct decision. Do not repeat the parent synopsis in every child.

<reference_routing_examples>

```markdown
<!-- BAD: a filename with no narrative relevance. -->

- [Validation](./references/validation.md)
```

```markdown
<!-- BAD: an artificial command that still does not explain the decision. -->

- For validation, use [validation](./references/validation.md).
```

```markdown
<!-- BAD: a miniature reference defeats selective loading. -->

- When validation is needed, read [validation](./references/validation.md), which covers link checking, formatting, schemas, examples, forward tests, and more.
```

```markdown
<!-- GOOD: an inline router makes the situation and protection relevant. -->

- When examples can drift from their claimed API, apply the [semantic validation gate](./references/validation.md) before release.
```

```markdown
<!-- GOOD: a parent hook organizes several progressively disclosed decisions. -->

1. Keep untrusted data outside the domain until runtime evidence proves its shape.
   - [Validate serialized input at the controlled boundary.](./references/input-validation.md)
   - [Preserve validation failures as explicit outcomes.](./references/validation-failures.md)
```

</reference_routing_examples>

Keep each router to one sentence. A parent hook may add enough prose and hierarchy to communicate the governing opinion. The linked reference owns the procedural details.
