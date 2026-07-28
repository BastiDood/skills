---
name: python-best-practices
description: Opinionated Python 3.13+ conventions for application and library code. Use when writing or reviewing Python typing, protocols, generics, finite states, optional values, exceptions, serialized-data validation, package exports, import layout, typed distributions, async resource ownership, async pagination, or per-distribution packaging metadata.
---

# Python Best Practices

Read as many linked references as are relevant to the current task before writing or reviewing Python.

## References

- For obvious types, prefer [inference](./references/inference-and-annotations.md).
- For behavioral contracts, use [protocols](./references/protocols.md).
- For related generic types, use [PEP 695 generics](./references/pep-695-generics.md).
- For finite states, require [exhaustive handling](./references/finite-state-exhaustiveness.md).
- For `T | None` and other union types, [narrow optional values](./references/optional-narrowing.md).
- For missing required values, [do not fabricate defaults](./references/no-fabricated-defaults.md).
- For runtime validation, [do not use assertions](./references/no-runtime-asserts.md).
- For serialized input, apply [boundary validation](./references/boundary-validation.md).
- For expected failures, use [narrow exception handling](./references/exception-handling.md).
- For package APIs, define [explicit public exports](./references/public-exports.md).
- For installable code, follow [Python naming and `src` layout](./references/naming-and-src-layout.md).
- For published typing, use [`py.typed` and stubs](./references/py-typed-and-stubs.md).
- For async resources, use [async context managers](./references/async-context-managers.md).
- For provider pages, expose [async pagination](./references/async-pagination.md).
- For buildable packages, declare [per-distribution metadata](./references/project-metadata.md).
- For repository imports, [do not mutate import paths](./references/no-import-path-mutation.md).
