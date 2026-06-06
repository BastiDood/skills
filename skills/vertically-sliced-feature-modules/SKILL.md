---
name: vertically-sliced-feature-modules
description: Use when planning, refactoring, and reviewing towards a vertically sliced architecture of "feature modules", especially when deciding whether code belongs in a feature slice, shared module, route/page entry point, technical layer, or nested subsystem.
---

# Vertical Slice Feature Modules

Use vertical feature slices instead of horizontal technical layers. A feature module owns its end-to-end behavior: UI, state, actions, validation, domain model, feature-specific helpers, and tests that exist only for that feature.

## Core Rule

- Slice by feature first, not by technical role.
- Avoid global technical-layer folders that scatter one feature across distant directories, such as global `models/`, `views/`, `controllers/`, `components/`, `hooks/`, or `actions/` folders for feature-specific code.
- Keep all code for a feature close enough that a developer can understand, change, and test the feature without hunting across unrelated layers.
- A vertical slice is correct when it lowers coupling between features and raises cohesion inside the feature.

## Feature Ownership

- A feature module must encapsulate only its own end-to-end logic.
- Feature-specific code belongs inside the feature, even when it has different technical roles.
- Technical separation is still valid inside the feature. Local files such as `model`, `view`, `state`, `actions`, `context`, `schema`, or `components` are fine when the feature entry point owns and imports them.
- Do not let a feature module become a junk drawer for adjacent features.
- Do not create broad sibling folders that float beside the real owner. If a folder is only used by one feature entry point, nest it under that feature.

## Entry Points

- Treat route/page/app files as orchestrators.
- Route/page/app entry points should compose feature modules and shared infrastructure; they should not hold feature-specific internals.
- A feature entry point must own and import its implementation subtree.
- From the perspective of an entry point, it should not have sibling implementation files or folders that it does not import.
- Do not import a module's entry point from inside the same module. That creates a high-risk path to cyclical dependencies.
- If internal code needs logic from the entry point, extract that logic into a bespoke utility file or merge it into an existing internal utility. Import and test that helper directly.
- The entry point should stand on its own as the public composition surface, not as an internal dependency.
- Before a large refactor, write the intended ownership tree and verify that each entry point imports the subtree it claims to own.

## Shared Modules

- Extract shared code only when it is genuinely shared across multiple features.
- Shared modules are for common UI primitives, infrastructure, data access, cross-feature domain concepts, utilities, and integration boundaries.
- Bespoke feature behavior stays isolated inside the feature until more than one feature truly needs it.
- Do not move code into shared modules merely because it has a familiar technical role.
- Cross-feature imports should target stable shared modules or public feature entry points, not private implementation leaves.

## Nested Subsystems

- Prefer deeper owned hierarchy over broad sibling folders when a subsystem is only consumed by one parent.
- A nested subsystem should be self-contained: its entry point imports its private implementation files, and callers import only the subsystem entry point.
- Subsystems should import downward or sideways inside their owned subtree. Avoid traversing upward into parent internals.
- If two siblings need the same concept, ask whether that concept belongs in their parent domain module.
- If a supposed module has no entry point, or its entry point does not import its siblings, the structure is suspect.

## Placement Test

When deciding where code belongs, ask:

- Which feature changes when this code changes?
- Is this code needed by more than one feature today?
- Can the owning entry point import this code without reaching outside its subtree?
- Does this placement reduce navigation when debugging the feature?
- Does this placement reduce coupling between unrelated features?
- Does this placement keep feature-specific logic out of global technical layers?

If the answers are unclear, keep the code closer to the feature until real sharing appears.

## Refactor Discipline

- Read the existing module tree before creating new folders.
- Compare proposed structure against the current ownership graph.
- Move existing files when possible instead of recreating them.
- Preserve behavior while changing structure.
- Delete obsolete files only after confirming no entry point or test still owns them.
- Keep tests collocated with the code they verify.
- Avoid plain re-export barrels when the codebase expects direct imports.

## Boundary Limits

- Do not force vertical slicing across fundamentally separate ecosystems when build tools, deployment, language boundaries, or runtime boundaries make locality more expensive than useful.
- Keep vertical slices within a coherent build/runtime boundary unless the project already supports cross-ecosystem feature modules cleanly.

## Review Smells

- A feature requires edits in many distant technical-layer directories.
- A route/page contains business logic that belongs to a feature module.
- A shared module contains behavior used by only one feature.
- A feature imports another feature's private leaf files.
- Sibling folders exist beside an entry point but are not imported by that entry point.
- Internal files import their own module's entry point.
- A module name describes a technical role globally instead of a feature or owned subsystem.
- A helper was extracted before a second feature needed it.
