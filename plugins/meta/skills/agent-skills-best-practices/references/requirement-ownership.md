# Requirement Ownership

State each requirement once, inlined in the section or file that owns it. A restated rule spends context on every invocation, and the copies drift apart when one of them is revised.

- Do not close a skill with a recap or `Rules` list that repeats earlier sections.
- Do not warn against a mistake that an exact command or example already prevents. The correct command is the instruction.
- Let `SKILL.md` name a template, prompt, or script and say what to do with its result. The linked file owns its content, so `SKILL.md` does not paraphrase it.

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

</requirement_ownership_examples>

Before you delete a repeated clause, find its owner and confirm that the owner keeps every condition, exception, and level of obligation.
