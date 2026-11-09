---
name: plugin-best-practices
description: Plugin design conventions for public outcomes, private implementation, and minimal packaging. Use when creating, editing, restructuring, or reviewing Agent Plugins or Claude Plugins.
---

# Plugin Best Practices

Prefer fine-grained plugins that package one coherent installable capability or body of guidance. Consumers install the plugin as a unit instead of assembling cooperating skills or accepting unrelated functionality.

Treat a plugin as an encapsulation boundary. Like a class in object-oriented programming, it exposes a deliberate public interface while hiding implementation details that consumers do not need to understand or invoke.

## Effective Strategies for Focused Agent-Skill Plugins

Follow the guidelines below. Read each linked reference that applies to the plugin design task.

1. Expose public outcomes while keeping implementation skills private.
   - [Hide implementation skills as flat siblings that are not user-invocable.](./references/plugin-structure.md)
2. Package only the metadata and files that consumers need.
   - [Declare only the manifest fields that consumers read.](./references/plugin-manifests.md)
