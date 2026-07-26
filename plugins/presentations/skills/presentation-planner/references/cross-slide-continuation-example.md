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

Split the list without repeating its parent or promoting its remaining items. Keep the incoming items at level 1, while copying the first as a level-0 outgoing preview.

</rule_demonstrated>
