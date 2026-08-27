# Actor Identity and Secrets

Partition the cache by stable actor or session identity when identity changes response visibility.

Use a non-secret actor ID, tenant ID, or stable session partition. Never include bearer tokens, API keys, cookies, authorization codes, or other credentials in a query key; keys can appear in developer tools, logs, persistence, and diagnostics.

```typescript
// BAD: credential material becomes cache metadata.
const queryKey = ['jobs', accessToken] as const;

// GOOD: stable visibility partition without a secret.
const queryKey = ['jobs', actor.id] as const;
```

Let the HTTP client attach credentials to the request. Cache identity represents visibility, not credential material. Remove or invalidate actor-scoped data when the authenticated actor changes according to application security policy.
