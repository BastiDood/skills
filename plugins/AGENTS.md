# Plugin Development Workflow

Whenever you complete a feature, bug fix, or refactor:

1. Run the Oxfmt formatter with `bun run fmt:fix`.
2. Bump the `version` of the plugin in its respective manifest file (i.e., `.{claude,cursor}-plugin/plugin.json`).

## Library Skill Sources

Every skill dedicated to an external library must include a `Library Sources` section in `SKILL.md` with:

- GitHub repository ID.
- Context7 library ID.
- DeepWiki repository ID.

Keep this lookup metadata in `SKILL.md`, not a reference file. Do not invent an unavailable identifier.
