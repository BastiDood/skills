# Cross-Slide Continuation

<original_verbatim_text>

Context is what makes or breaks your experience with LLMs. The context contains:

- The system prompt
- The MCP descriptions
- The agent skills
- The user messages

Each item consumes part of the context window.

</original_verbatim_text>

<expected_on_slide_content>

Slide 1:

- The **context** contains:
  - The system prompt
  - The MCP descriptions

Slide 2:

- Each item consumes part of the **context window**.

</expected_on_slide_content>

<expected_presenter_notes>

```text
Slide 1:

- Context is what makes or breaks your experience with LLMs.
- The context contains:
  - The system prompt
  - The MCP descriptions
- (The agent skills…)

Slide 2:

  - The agent skills
  - The user messages
- Each item consumes part of the context window.
```

</expected_presenter_notes>

<rule_demonstrated>

The slide boundary divides one source-authored list for pacing while preserving the list's semantic parent. The next slide therefore begins at level 1. Do not repeat `The context contains:` or promote the remaining list items to level 0 merely to make the second slide's notes self-contained. The outgoing slide copies the next slide's first nested item as a level-0 parenthesized preview, but the incoming item retains its semantic indentation. The visible slide can select the concluding source phrase when the inherited list relationship is already clear from the preceding slide.

</rule_demonstrated>
