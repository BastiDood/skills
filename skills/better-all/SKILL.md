---
name: better-all
description: Use the `better-all` library for `Promise.all` with automatic DAG-based dependency optimization and full type inference. Use when parallelizing async operations with complex dependencies.
---

## Documentation

- If available, use Context7 (Library ID: `/shuding/better-all`) to fetch the latest documentation.
- If available, use DeepWiki (GitHub Repository: `shuding/better-all`) for implementation details.

# better-all Library

`better-all` provides `Promise.all` with automatic dependency optimization. Instead of manually analyzing which tasks can run in parallel, tasks declare dependencies inline and execution is automatically optimized.

## Installation

```bash
pnpm add better-all
```

## Basic Usage

```typescript
import { all } from 'better-all';

const results = await all({
	// Independent tasks run in parallel
	fetchUser: async () => await fetchUser(userId),
	fetchPosts: async () => await fetchPosts(userId),

	// Dependent task waits automatically
	combined: async ctx => {
		const user = await ctx.$.fetchUser;
		const posts = await ctx.$.fetchPosts;
		return { user, posts };
	},
});

// results.fetchUser, results.fetchPosts, results.combined all typed
```

## Key Advantage: Automatic Optimization

```typescript
// Manual approach - error-prone
const [user, posts] = await Promise.all([fetchUser(), fetchPosts()]);
const profile = await buildProfile(user, posts);
const [feed, stats] = await Promise.all([buildFeed(profile, posts), buildStats(profile)]);

// better-all - dependencies declared, execution optimized
const results = await all({
	user: () => fetchUser(),
	posts: () => fetchPosts(),
	profile: async ctx => buildProfile(await ctx.$.user, await ctx.$.posts),
	feed: async ctx => buildFeed(await ctx.$.profile, await ctx.$.posts),
	stats: async ctx => buildStats(await ctx.$.profile),
});
```

## Type Inference

Results are fully typed based on task return types:

```typescript
const results = await all({
	count: () => Promise.resolve(42),
	name: () => Promise.resolve('test'),
	combined: async ctx => ({
		count: await ctx.$.count,
		name: await ctx.$.name,
	}),
});

// TypeScript knows:
// results.count: number
// results.name: string
// results.combined: { count: number; name: string }
```
