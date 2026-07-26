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

- System prompt
- MCP descriptions

Slide 2:

- Agent skills
- User messages
- Every item consumes **context**

</expected_on_slide_content>

<expected_presenter_notes>

```text
Slide 1:

- Context is what makes or breaks your experience with LLMs.
- The context contains:
  - The system prompt
  - The MCP descriptions
- (The agent skills...)

Slide 2:

  - The agent skills
  - The user messages
- Each item consumes part of the context window.
```

</expected_presenter_notes>

<anti_example>

Slide 2 presenter notes:

- The context contains:
  - The agent skills
  - The user messages
- Each item consumes part of the context window.

</anti_example>

<why_the_anti_example_fails>

It repeats a parent that belongs to the previous slide solely to make the incoming list look self-contained. Visible copy may reframe the continuation compactly, but presenter-note indentation must preserve the original semantic continuation.

</why_the_anti_example_fails>

<rule_demonstrated>

Split the presenter-note list without repeating its parent or promoting its remaining items. Keep the incoming note items at level 1, while the slide itself may use compact labels and the outgoing preview remains level 0.

</rule_demonstrated>
