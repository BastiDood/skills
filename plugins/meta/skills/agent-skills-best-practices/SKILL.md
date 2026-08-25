---
name: agent-skills-best-practices
description: Agent-skill design conventions for focused scope, precise discovery, and progressive disclosure. Use when creating, restructuring, or reviewing `SKILL.md`-based skills.
---

# Agent Skills Best Practices

Treat agent skills as opinionated context engineering, not as mirrors of documentation that an agent can fetch elsewhere. A skill earns its context cost by preserving distinctive judgment, repeatable procedure, or peripheral knowledge that materially changes how an agent works.

Keep the governing model and the minimum explainer needed to understand a skill in `SKILL.md`. Inline content that every invocation needs. Reserve progressive disclosure for conditional or detail-oriented guidance, and route to that guidance with concise prose that explains why it matters.

## Effective Strategies for Authoring Skills

Follow the guidelines below when authoring or reviewing a skill. Read each linked reference that applies to the current task.

1. Make the capability legible at discovery and invocation time.
   - [Choose a procedural or documentary name that communicates what the skill provides.](./references/skill-naming.md)
   - [Write a trigger description that names the capability and the concrete situations that need it.](./references/trigger-descriptions.md)
2. Keep each skill cohesive and its loaded context focused on decisions that materially change agent behavior.
   - [Split unrelated triggers or decisions instead of growing an umbrella skill.](./references/skill-scope.md)
   - [Separate the always-loaded governing model from conditional detail.](./references/context-and-disclosure.md)
   - [Route to conditional references with the triggering situation and governing opinion.](./references/reference-routing.md)
   - [Use examples to expose the opinion without turning the skill into a tutorial.](./references/examples.md)
   - [Keep prose direct, compact, and mechanically consistent.](./references/prose-and-formatting.md)
3. Default deterministic work to scripts and reserve model reasoning for judgment that cannot be encoded as exact inputs and outputs. Prefer existing and standard-library primitives; when Python genuinely needs a third-party dependency, declare it with [PEP 723](https://peps.python.org/pep-0723/) and lock it with `uv`.
   - [Use the reproducible, read-only-compatible executable-script workflow.](./references/executable-scripts.md)
4. Write guidance so its decisions survive outside the context that produced them. Preserve concrete judgment without depending on a particular user, project, machine, prior engagement, or unavailable artifact.
   - [Remove incidental context while retaining concrete admission and validation rules.](./references/agnostic-references.md)
5. Keep conditional resources portable, navigable, and owned by the narrowest relevant skill.
   - [Keep resource paths flat and one hop from the entry point.](./references/directory-structure.md)
   - [Identify governed external libraries without copying their documentation.](./references/library-sources.md)
   - [Apply the distribution and licensing policy before bundling static assets.](./references/asset-distribution.md)
