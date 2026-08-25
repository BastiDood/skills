# Entry Points and Composition

Treat route, page, application, and runtime entry points as composition surfaces. They select features, provide framework context, acquire resources, and register executable capabilities.

```typescript
// BAD: a feature imports and orchestrates a peer feature.
import { showSubmissionReceipt } from '@/features/receipts';

export async function submitApplication(input: ApplicationInput) {
	const application = await persistApplication(input);
	return showSubmissionReceipt(application);
}

// GOOD: page.tsx owns cross-feature composition.
import { submitApplication } from '@/features/applications';
import { showSubmissionReceipt } from '@/features/receipts';

export async function submitFromPage(input: ApplicationInput) {
	const application = await submitApplication(input);
	return showSubmissionReceipt(application);
}
```

A feature or subsystem entry is a public composition surface for one cohesive capability. It should export only an orchestrator wrapper when an entry-level wrapper is necessary for sequencing, dependency injection, resource ownership, or another real boundary. An entry that only re-exports implementation leaves is a barrel, not an architectural boundary; flatten a ceremonial directory when it has no public boundary or orchestration to own.

An internal `import "."`, module-root import, package-root import, or equivalent self-import is an architectural smell because it hides the actual dependency direction and often indicates that the entry is a barrel. Do not retain it: extract the needed testable behavior into a named private sibling module. The entry and other private siblings import that lower module directly; external callers import the entry.

Thin presentation routes normally render one feature entry. A caller such as `index.ts`, `mod.rs`, `__init__.py`, `__main__.py`, `page.tsx`, `+page.svelte`, or `+layout.svelte` can compose multiple isolated features when the screen or route owns the combined outcome.

Framework-owned server routes can retain authorization, request parsing, form actions, transaction coordination, and response construction when those responsibilities belong to the transport boundary.

Runtime registries and application roots can import many features. They own sequencing and dependency injection, but they must not absorb the business logic of the capabilities they register.
