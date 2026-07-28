# State Machines

Model a finite UI flow as one discriminated state machine. Do not combine parallel flags and optional values that can describe contradictory states.

Keep the `const enum` and its state contracts in a TypeScript module. A Svelte component imports the finished finite-state contract instead of asking the component compiler to transform the enum.

```typescript
// lobby-state.ts
// GOOD: one discriminant determines every valid state.
export const enum LobbyStatus {
	Idle = 0,
	Joining = 1,
	Active = 2,
	Failed = 3,
}

interface IdleLobby {
	status: LobbyStatus.Idle;
}

interface JoiningLobby {
	status: LobbyStatus.Joining;
}

interface ActiveLobby {
	status: LobbyStatus.Active;
	lobbyId: string;
}

interface FailedLobby {
	status: LobbyStatus.Failed;
	message: string;
}

export type LobbyState = IdleLobby | JoiningLobby | ActiveLobby | FailedLobby;

export function getLobbyLabel(state: LobbyState) {
	switch (state.status) {
		case LobbyStatus.Idle:
			return 'Ready to join';
		case LobbyStatus.Joining:
			return 'Joining';
		case LobbyStatus.Active:
			return `Connected to ${state.lobbyId}`;
		case LobbyStatus.Failed:
			return state.message;
		default: {
			throw new Error('unexpected lobby state');
		}
	}
}
```

```svelte
<!-- GOOD: Import the Finished State Contract -->

<!-- Lobby.svelte -->
<script lang="ts">
	import { LobbyStatus, type LobbyState } from './lobby-state';

	let lobby = $state<LobbyState>({ status: LobbyStatus.Idle });
</script>
```

```svelte
<!-- BAD: Parallel State Can Contradict Itself -->

<script lang="ts">
	let isJoining = $state(false);
	let lobbyId = $state<string | undefined>();
	let failureMessage = $state<string | undefined>();
</script>
```
