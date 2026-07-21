# Plugin Development Workflow

Whenever you complete a feature, bug fix, or refactor:

1. Run the Oxfmt formatter with `bun run fmt:fix`.
2. Bump the `version` of the plugin in its respective manifest file (i.e., `.{claude,cursor}-plugin/plugin.json`).
