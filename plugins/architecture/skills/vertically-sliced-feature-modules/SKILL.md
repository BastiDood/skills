---
name: vertically-sliced-feature-modules
description: Opinionated feature-first architecture conventions for cohesive modules, owned subtrees, entry points, shared-code promotion, and package boundaries. Use when designing or reviewing directory structure, placing UI or workflow code, splitting nested capabilities, extracting shared modules, enforcing feature import boundaries, deciding whether a feature deserves a package, or refactoring horizontal technical layers.
---

# Vertically Sliced Feature Modules

This skill organizes systems around capabilities that own their implementation end to end. Feature roots, nested subsystems, compositional entry points, and deliberately promoted shared boundaries keep cohesion high and dependency direction visible.

Reconstruct the current ownership graph and build/runtime boundaries before proposing folders. Treat the example trees as adaptable ownership shapes, not prescribed filenames.

## Feature Ownership

A feature owns one user-visible or business capability end to end. Keep its technical roles inside that owner instead of scattering them across global layers.

Features are isolated siblings and do not import one another. Callers and orchestrators compose feature entries. When multiple features need the same stable logic, promote it to a focused shared owner instead of making either feature the dependency of the other.

## References

Read as many linked references as are relevant to the current task before planning, moving, or reviewing modules.

- For feature-first topology, use [directory structure](./references/directory-structure.md).
- For routes and runtime registries, keep [entry points compositional](./references/entry-points-and-composition.md).
- For private capabilities, prefer [nested subsystems](./references/nested-subsystems.md).
- For cross-boundary access, define [public contracts and import direction](./references/public-contracts-and-import-direction.md).
- Before extracting common code, apply [shared-code promotion](./references/shared-code-promotion.md).
- Before creating a distribution, apply [package admission](./references/package-admission.md).
- For feature-owned coverage, use [test placement](./references/test-placement.md).
- During structural changes, apply [refactor and review discipline](./references/refactor-and-review.md).
