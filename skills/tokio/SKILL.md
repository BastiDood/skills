---
name: tokio
description: Async runtime patterns for Rust with Tokio. Use when working with async tasks, signals, channels, networking, or concurrency.
---

# Tokio Async Runtime

Async runtime with multi-threaded executor, signal handling, and synchronization primitives.

## Documentation

- If available, use `context7` (Library ID: `tokio-rs-tokio`) to fetch the latest documentation.
- If available, use `deepwiki` (GitHub Repository: `tokio-rs/tokio`) for implementation details.

## Runtime Initialization

```rust
use tokio::runtime::Builder;

Builder::new_multi_thread()
    .enable_io()
    .enable_time()
    .build()?
    .block_on(async { /* ... */ })
```

## Task Management with JoinSet

Prefer `JoinSet` over raw `tokio::spawn` for coordinated task management:

```rust
use tokio::task::JoinSet;

let mut tasks = JoinSet::new();
tasks.spawn(handle_tcp(port, cancel.clone()));
tasks.spawn(handle_udp(port, cancel.clone()));

while let Some(result) = tasks.join_next().await {
    // Handle task completion
}
```

## Signal Handling & Graceful Shutdown

Use `CancellationToken` for coordinated shutdown across actors:

```rust
use tokio::signal::ctrl_c;
use tokio_util::sync::CancellationToken;

let cancel = CancellationToken::new();

tokio::select! {
    _ = ctrl_c() => cancel.cancel(),
    result = tasks.join_next() => { /* handle */ }
}
```

## Watch Channels for Broadcasting

Use `watch` channels for broadcast scenarios (not `mpsc`):

```rust
use tokio::sync::watch;

let (tx, rx) = watch::channel(initial_value);

// Sender side
tx.send_modify(|data| { /* mutate */ });

// Receiver side (multiple consumers)
let mut rx = rx.clone();
rx.changed().await?;
let snapshot = rx.borrow().clone();
```

## TCP Listener Pattern

```rust
use tokio::net::TcpListener;

let listener = TcpListener::bind(("0.0.0.0", port)).await?;
loop {
    let (stream, addr) = listener.accept().await?;
    tokio::spawn(handle_connection(stream, addr));
}
```

## Best Practices

1. **Always use `CancellationToken`** for coordinated shutdown across actors
2. **Prefer `JoinSet`** over raw `tokio::spawn` for task coordination
3. **Use `watch` channels** for broadcast scenarios (not `mpsc`)
4. **Enable only needed features** to reduce compile time
5. **Use `select!`** for racing between signals and async operations
