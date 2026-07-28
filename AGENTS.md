# Repository Conventions

## Package Management

Use Bun for package installation, dependency management, and package scripts.

## Plugin Manifests

- Use `.claude-plugin/plugin.json` as the canonical plugin manifest.
  - Do not create a separate `.codex-plugin/` directory or manifest. Codex already falls back to the `.claude-plugin/*` directory anyway.
- Keep the canonical manifest minimal: declare only the plugin name, version, display name, and available logo fields.
- Keep `.cursor-plugin/plugin.json` only as the lightweight Cursor compatibility manifest.
