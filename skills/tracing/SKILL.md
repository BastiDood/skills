---
name: tracing
description: Structured logging and instrumentation for Rust with the tracing crate. Use when adding logging, debugging, spans, or instrumentation.
---

# Tracing - Structured Logging

Structured logging with hierarchical spans and environment-controlled filtering.

**Libraries:** tracing, tracing-subscriber, tracing-tree

## Documentation

- If available, use `context7` (Library ID: `tokio-rs-tracing`) to fetch the latest documentation.
- If available, use `deepwiki` (GitHub Repository: `tokio-rs/tracing`) for implementation details.

## Logging Setup

```rust
use tracing_subscriber::{layer::SubscriberExt, util::SubscriberInitExt};
use tracing_tree::HierarchicalLayer;

pub fn init() {
    tracing_subscriber::registry()
        .with(
            HierarchicalLayer::new(4)
                .with_targets(true)
                .with_thread_ids(true)
                .with_thread_names(true),
        )
        .with(EnvFilter::from_default_env())
        .init();
}
```

## Function Instrumentation

```rust
use tracing::{debug, error, info, instrument, trace, warn};

#[instrument(skip(self, stream))]
async fn handle_connection(&self, stream: TcpStream) -> Result<()> {
    info!("new connection");
    // ...
}

#[instrument(skip_all, fields(peer = %addr))]
async fn process_peer(&self, addr: SocketAddr, ws: WebSocketStream) {
    debug!("processing peer");
}
```

## Span Creation and Linking

```rust
use tracing::{info_span, Span, Instrument};

let span = info_span!("lobby", id = %lobby_id);

async move {
    // ...
}.instrument(span).await;

// Link causality
let child_span = info_span!("peer_handler");
child_span.follows_from(Span::current());
```

## Contextual Logging

```rust
// Basic levels
trace!("entered function");
debug!("parsed message");
info!("connection established");
warn!("retrying operation");
error!("failed to process");

// With structured fields
info!(peer = %name, "peer joined lobby");
debug!(bytes = data.len(), "received message");
error!(%error, "connection failed");

// Display vs Debug formatting
info!(%display_value);  // Uses Display trait
info!(?debug_value);    // Uses Debug trait
```

## Best Practices

1. **Use `#[instrument]`** on async functions for automatic span creation
2. **Skip large arguments** with `skip(large_arg)` or `skip_all`
3. **Add meaningful fields** to spans: `fields(peer = %addr)`
4. **Use appropriate levels**:
   - `trace` — Very verbose, function entry/exit
   - `debug` — Detailed debugging info
   - `info` — Notable events (connections, completions)
   - `warn` — Recoverable issues
   - `error` — Failures requiring attention
5. **Link related spans** with `.follows_from()` for causality
6. **Use `%` for Display, `?` for Debug** formatting

## Environment Configuration

```bash
# Show all logs from your crates at trace level
RUST_LOG='my_crate=trace' cargo run

# Show info for everything, debug for specific crate
RUST_LOG='info,my_crate_proto=debug' cargo run

# Multiple targets
RUST_LOG='my_crate=trace,tokio=warn,hyper=info' cargo run
```

## Feature Configuration

```toml
[dependencies.tracing]
version = "0.1"
default-features = false
features = ["attributes"]  # For #[instrument]
```
