[See Documentation](https://docs.rs/axum/latest/axum/extract/ws/index.html)
### Basic Example
```rust
use axum::{
    extract::ws::{WebSocketUpgrade, WebSocket},
    routing::any,
    response::{IntoResponse, Response},
    Router,
};

let app = Router::new().route("/ws", any(handler));

async fn handler(ws: WebSocketUpgrade) -> Response {
    ws.on_upgrade(handle_socket)
}

async fn handle_socket(mut socket: WebSocket) {
    while let Some(msg) = socket.recv().await {
        let msg = if let Ok(msg) = msg {
            msg
        } else {
            // client disconnected
            return;
        };

        if socket.send(msg).await.is_err() {
            // client disconnected
            return;
        }
    }
}
```

### Passing Data and/or State to `on_upgrade`
```rust
use axum::{
    extract::{ws::{WebSocketUpgrade, WebSocket}, State},
    response::Response,
    routing::any,
    Router,
};

#[derive(Clone)]
struct AppState {
    // ...
}

async fn handler(ws: WebSocketUpgrade, State(state): State<AppState>) -> Response {
    ws.on_upgrade(|socket| handle_socket(socket, state))
}

async fn handle_socket(socket: WebSocket, state: AppState) {
    // ...
}

let app = Router::new()
    .route("/ws", any(handler))
    .with_state(AppState { /* ... */ });
```

### Read and Write Concurrently

If you need to reed and write concurrently from a `WebSocket`, you can use `StreamExt::split`

```rust
use axum::{Error, extract::ws::{WebSocket, Message}};
use futures_util::{sink::SinkExt, stream::{StreamExt, SplitSink, SplitStream}};

async fn handle_socket(mut socket: WebSocket) {
    let (mut sender, mut receiver) = socket.split();

    tokio::spawn(write(sender));
    tokio::spawn(read(receiver));
}

async fn read(receiver: SplitStream<WebSocket>) {
    // ...
}

async fn write(sender: SplitSink<WebSocket, Message>) {
    // ...
}
```

### SplitStream
