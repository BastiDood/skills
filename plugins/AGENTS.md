# Plugin Development Workflow

Whenever you complete a feature, bug fix, or refactor:

1. Run the Oxfmt formatter with `bun run fmt:fix`.
2. Bump the `version` in both `plugin.json` and `.claude-plugin/plugin.json`.

## Library Skill Sources

Every skill dedicated to an external library must include a `Library Sources` section in `SKILL.md` with:

- GitHub repository ID.
- Context7 library ID.
- DeepWiki repository ID.

Keep this lookup metadata in `SKILL.md`, not a reference file. Do not invent an unavailable identifier.

## Skill Context and Reference Routing

- Keep `SKILL.md` focused on the critical context and explainer required to understand the skill.
- Inline guidance that every invocation needs. Reserve references for conditionally applicable or detail-oriented context.
- Put the reference-consumption instruction inside the `References` section.
- Route references with concise, natural prose that explains the situation, decision, or failure mode that makes each link relevant.
- Hyperlink the relevant concept in place.
  - Do not reduce routers to filenames, topic labels, or `use XYZ` commands.
  - Do not expand them into documentation paragraphs.
- Index every reference exactly once from `SKILL.md` with an explicit `./` path. Keep each reference self-contained instead of linking it to other references or skills.
