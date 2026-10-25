# Server Form Decoding

Place decoding with the boundary that owns the incoming representation. A helper that imports server credentials, persistence, or server-only modules stays server-only. A pure `FormData` converter may live in a shared feature module when both browser and server boundaries need the same conversion.

```typescript
// A shared converter depends only on the representation library.
import { decode } from 'decode-formdata';

export function decodeCreateItemForm(formData: FormData) {
	return decode(formData, {
		numbers: ['amount', 'quantity'],
		dates: ['datedAt', 'expiresAt'],
		arrays: ['tags', 'categories'],
	});
}
```

Decoding converts representations; it does not validate or authorize them. The server action validates external request input even when the browser caller is typed or already validated it. Pass the resulting trusted value to internal domain work without reparsing it.
