---
name: better-all-best-practices
description: Use the `better-all` library for `Promise.all` with automatic DAG-based dependency optimization and full type inference. Use when parallelizing async operations with complex dependencies.
---

# `better-all` Library

`better-all` provides `Promise.all` with automatic dependency optimization. Instead of manually analyzing which tasks can run in parallel, tasks declare dependencies inline and execution is automatically optimized. The library exports `all`, `allSettled`, and `flow`.

## Basic Usage

Tasks access sibling results through `this.$`, so they MUST be written as method shorthand (or `function` expressions) — never arrow functions, which have no `this` binding and receive no arguments:

```typescript
import { all } from 'better-all';

const results = await all({
	// Independent tasks run in parallel
	async fetchUser() {
		return await fetchUser(userId);
	},
	async fetchPosts() {
		return await fetchPosts(userId);
	},

	// Dependent task waits automatically
	async combined() {
		const user = await this.$.fetchUser;
		const posts = await this.$.fetchPosts;
		return { user, posts };
	},
});

// results.fetchUser, results.fetchPosts, results.combined all typed
```

Each task also receives `this.$signal`, an `AbortSignal` that aborts when any sibling task fails.

## Key Advantage: Automatic Optimization

```typescript
// BAD - manual staging over-serializes and is error-prone
const [user, posts] = await Promise.all([fetchUser(), fetchPosts()]);
const profile = await buildProfile(user, posts);
const [feed, stats] = await Promise.all([buildFeed(profile, posts), buildStats(profile)]);

// GOOD - dependencies declared inline, execution optimized
const results = await all({
	async user() {
		return await fetchUser();
	},
	async posts() {
		return await fetchPosts();
	},
	async profile() {
		return buildProfile(await this.$.user, await this.$.posts);
	},
	async feed() {
		return buildFeed(await this.$.profile, await this.$.posts);
	},
	async stats() {
		return buildStats(await this.$.profile);
	},
});
```

## Type Inference

Results are fully typed based on task return types:

```typescript
const results = await all({
	async count() {
		return 42;
	},
	async name() {
		return 'test';
	},
	async combined() {
		return { count: await this.$.count, name: await this.$.name };
	},
});

// TypeScript knows:
// results.count: number
// results.name: string
// results.combined: { count: number; name: string }
```

## Documentation

- If available, use Context7 (Library ID: `/shuding/better-all`) to fetch the latest documentation.
- If available, use DeepWiki (GitHub Repository: `shuding/better-all`) for implementation details.
