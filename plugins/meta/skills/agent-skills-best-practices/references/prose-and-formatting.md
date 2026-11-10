# Prose and Formatting

Write direct, opinionated prose in the imperative style. State the decision and its consequence. Give a rule its reason when the reason is not obvious: the model generalizes from the reason, while a bare command covers only the literal case.

- Write action steps as commands such as "Change X". Suggestions such as "Consider X" produce advice instead of the action.
- Do not hedge before doing the research needed to settle the claim.
- Do not include version strings, changelogs, history notes, or end-of-prompt markers. They spend context on every invocation and change no behavior.

Avoid prefixing rules with `CRITICAL:` or `IMPORTANT:` or bolded directives. Modern LLMs follow instructions closely. Aggressive emphasis makes them over-apply the rule.

Invoke another skill as `Skill(plugin:skill)` when it ships in a plugin and as `Skill(skill)` when it is standalone. The plugin prefix identifies one skill even when another plugin uses the same skill name.

Use title case for headings. Let the renderer wrap prose instead of inserting manual line breaks.

Tag every code fence with its language. Use `text` for directory trees and plain output and `markdown` for prose templates. An untagged fence leaves the reader to guess whether the block is a command, a template, or output.

Write CLI flags in their long form (e.g., prefer `--flag` over `-F`) whenever the tool offers one. A long flag states its meaning without asking the reader or the model to recall what a single letter does. It also keeps look-alike short flags apart.

Wrap examples and source text that the model could mistake for instructions in an XML tag whose name contains an underscore, such as `<severity_examples>`. CommonMark does not accept an underscore in an HTML tag name. The renderer leaves such a tag as literal text for the model to read as a prompt delimiter, the convention prompts commonly use. A single-word tag such as `<examples>` reads as ordinary markup. Put each tag on its own line with a blank line on each side to keep it from merging into the adjacent paragraph.

Quote the name of a column, section, label, or category value that a template, reference, or tool output defines whenever prose mentions it. A table, a heading, a bold definition term such as **Safe:**, or a list item that opens with the name and a colon defines the name and stays unquoted. Its formatting already marks the name. Blending into the sentence, a bare capitalized name can read to the model as an ordinary word or an instruction rather than as a reference to the defined field. Keep backticks for code and for literal values that the agent types or matches, such as a flag, a key, or an output line; backticks around a label make it look like something to type.

<column_reference_examples>

```markdown
<!-- BAD: the column names blend into the prose. -->

Order the rows by Risk, then Impact, then package name.
```

```markdown
<!-- GOOD: quotes mark the names as references to the template's columns. -->

Order the rows by "Risk", then "Impact", then package name.
```

</column_reference_examples>

Treat each key term that a skill defines as a contract treats a defined term. Define it once in Title Case and reuse that exact casing at every mention. Capitalization separates the defined term from the ordinary phrase it resembles. The model reads "the target environment" as any environment that the task mentions, but it reads "Target Environment" as the one input that the skill defined. Keep the casing identical in every place that defines or fills the term, such as a list label, a prompt heading, and a template section. A term cased two ways reads as two terms. Leave a phrase in lowercase when the skill does not define it.

<defined_term_examples>

```markdown
<!-- BAD: the label and the template heading case one term two ways. -->

- **Target environment:** the deployment that receives the release.

## Target Environment
```

```markdown
<!-- GOOD: the label and the heading share one casing. -->

- **Target Environment:** the deployment that receives the release.

## Target Environment
```

</defined_term_examples>

Use ordinary ASCII quotes and apostrophes in Markdown. En dashes, em dashes, and directory-tree glyphs are acceptable when they improve readability.
