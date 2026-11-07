# Prose and Formatting

Write direct, opinionated prose in the imperative style. State the decision and its consequence. Give a rule its reason when the reason is not obvious: the model generalizes from the reason, while a bare command covers only the literal case.

- Write action steps as commands such as "Change X", not suggestions such as "Consider X", which produce advice instead of the action.
- Do not pad a convention with a summary of documentation.
- Do not hedge before doing the research needed to settle the claim.
- Do not include version strings, changelogs, history notes, or end-of-prompt markers. They spend context on every invocation and change no behavior.

Avoid prefixing rules with `CRITICAL:` or `IMPORTANT:` or bolded directives. Modern LLMs follow instructions closely, and aggressive emphasis makes them over-apply the rule.

Keep orientation prose short enough to prime the agent without becoming a second reference.

Invoke another skill as `Skill(plugin:skill)` when it ships in a plugin and as `Skill(skill)` when it is standalone. The plugin prefix identifies one skill even when another plugin uses the same skill name.

Use title case for headings. Let the renderer wrap prose instead of inserting manual line breaks.

Tag every code fence with its language. Use `text` for directory trees and plain output and `markdown` for prose templates. An untagged fence leaves the reader to guess whether the block is a command, a template, or output.

Write CLI flags in their long form (e.g., prefer `--flag` over `-F`) whenever the tool offers one. A long flag states its meaning, so neither the reader nor the model has to recall what a single letter does, and look-alike short flags stop being confusable.

Wrap examples and source text that the model could mistake for instructions in an XML tag whose name contains an underscore, such as `<severity_examples>`. CommonMark does not accept an underscore in an HTML tag name, so the renderer leaves the tag as literal text and the model reads it as a prompt delimiter, which is the convention prompts commonly use. A single-word tag such as `<examples>` reads as ordinary markup. Put each tag on its own line with a blank line on each side; otherwise it merges into the adjacent paragraph.

Use ordinary ASCII quotes and apostrophes in Markdown. En dashes, em dashes, and directory-tree glyphs are acceptable when they improve readability.
