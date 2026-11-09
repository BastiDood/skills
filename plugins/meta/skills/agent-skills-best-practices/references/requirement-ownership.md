# Requirement Ownership

State each requirement once, inlined in the section or file that owns it. A restated rule spends context on every invocation, and the copies drift apart when one of them is revised.

- Do not close a skill with a recap or `Rules` list that repeats earlier sections.
- Do not warn against a mistake that an exact command or example already prevents. The correct command is the instruction.
- Let `SKILL.md` name a template, prompt, or script and say what to do with its result. The linked file owns its content, so `SKILL.md` does not paraphrase it.
- Treat an invoked skill or a delegated prompt as a black box. Invoking it loads its directives, so the caller states only what the callee cannot know: the inputs, the caller's own policy, and what to do with the result. The callee states nothing about who calls it.

<requirement_ownership_examples>

````markdown
<!-- BAD: the prose repeats what the command shows, and a closing list restates the rule again. -->

Post the summary with `--field`, which reads the file into the field. Do not use `--raw-field`, which sends the literal path.

```bash
gh api repos/{owner}/{repo}/issues/42/comments --field body=@summary.md
```

## Rules

- Always post the summary with `--field`.
````

````markdown
<!-- GOOD: the directive is a comment inside the command block. No other section repeats it. -->

```bash
# Post the summary.
gh api repos/{owner}/{repo}/issues/42/comments --field body=@summary.md
```
````

```markdown
<!-- BAD: the caller repeats directives that the invoked skill and the delegated prompt already own. -->

1. Invoke `Skill(plugin:collect-context)` and read only the reference for the selected source.
2. Spawn one sub-agent per item with the [research prompt](./prompts/research-item.md). Each researcher works read-only.
```

```markdown
<!-- GOOD: the caller invokes each black box and adds only its own policy. -->

1. Invoke `Skill(plugin:collect-context)`.
2. For each item, fill every placeholder in the [research prompt](./prompts/research-item.md) and spawn one sub-agent with the filled prompt as its whole message. Launch every item at once and wait for all of them.
```

</requirement_ownership_examples>

Before you delete a repeated clause, find its owner and confirm that the owner keeps every condition, exception, and level of obligation.
