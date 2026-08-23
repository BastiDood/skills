# Repository Conventions

## Package Management

Use Bun for package installation, dependency management, and package scripts.

## Plugin Manifests

- The [Agent Plugins specification](https://agent-plugins.org/specification.md) is authoritative for plugin manifests.
  - The `.claude-plugin/plugin.json` is a compatibility manifest for Claude, which notably doesn't support the standard.
  - Do not create `.codex-plugin/plugin.json` or `.cursor-plugin/plugin.json`; the namespaced Agent Plugins extension is authoritative.
  - Do not create `.cursor-plugin/plugin.json` unless the plugin requires Cursor-specific components that Agent Plugins does not support.
