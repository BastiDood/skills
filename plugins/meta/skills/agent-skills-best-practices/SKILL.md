---
name: agent-skills-best-practices
description: Agent Skills design conventions for focused scope, precise discovery, and progressive disclosure. Use when creating, editing, restructuring, or reviewing `SKILL.md`-based skills.
---

# Agent Skills Best Practices

Use skills for decisions, procedures, and knowledge that change how the agent works. Do not repeat documentation it can look up.

## References

Read each linked reference that applies to the current skill-design task.

1. Make the skill easy to find and invoke.
   - [Name the procedure or subject the skill covers.](./references/skill-naming.md)
   - [Describe what the skill does and when to invoke it.](./references/trigger-descriptions.md)
2. Keep each skill focused.
   - [Split unrelated triggers or decisions into separate skills.](./references/skill-scope.md)
   - [Separate essential guidance from conditional detail.](./references/context-and-disclosure.md)
   - [Explain when each reference applies.](./references/reference-routing.md)
   - [State each requirement once, in the section or file that owns it.](./references/requirement-ownership.md)
   - [Contrast approaches and explain why the difference matters.](./references/examples.md)
   - [Keep prose direct, formatting consistent, and examples fenced apart from instructions.](./references/prose-and-formatting.md)
3. Use scripts for deterministic work and models for judgment.
   - [Lock script dependencies before release and keep scripts usable from read-only installations.](./references/executable-scripts.md)
   - [Define classification schemes as label tables calibrated with few-shot example tables that span their labels.](./references/judgment-calibration.md)
4. Delegate to sub-agents only to isolate context or run independent work in parallel.
   - [Write delegated prompts as placeholder templates that hydrate into self-contained messages.](./references/delegated-prompts.md)
5. Make guidance usable outside its source project.
   - [Separate shared principles from framework-specific examples. Resolve infrastructure identifiers at runtime.](./references/agnostic-references.md)
6. Keep resources portable and in the skill that owns them.
   - [Keep templates and other resources in `assets/`, one hop from the entry point.](./references/directory-structure.md)
   - [Identify external libraries without copying their documentation.](./references/library-sources.md)
   - [Check distribution and licensing rules before bundling third-party assets.](./references/asset-distribution.md)
