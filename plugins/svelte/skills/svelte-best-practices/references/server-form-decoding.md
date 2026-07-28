# Server Form Decoding

Keep the project `FormData` decoding helper in a server-only module and call it from SvelteKit actions. Do not import the helper into a browser component.

```typescript
// BAD: a browser component imports a server-only representation boundary.
import { decodeFormData } from '$lib/form-data.server';

// GOOD: only server actions import and run the helper.
// form-data.server.ts
export function decodeFormData(formData: FormData) {
	return decode(formData, {
		numbers: ['amount', 'quantity'],
		dates: ['datedAt', 'expiresAt'],
		arrays: ['tags', 'categories'],
	});
}
```

The decoder only performs representation conversion. The action still validates its result at the external-input boundary.
