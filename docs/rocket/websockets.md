## Installation
---
```rust
[dependencies]
ws = { package = "rocket_ws", version = "0.1.1" }
```

## Reading and Writing
---
To create a read/write channel to the client and call `handler`, we use `pub fn channel`

The `Channel` may borrow from the request. If it does, the lifetime should be specified as something other than `'static`. Otherwise, the `'static` lifetime should be used.

### Reading
```rust
while let Some(message) = stream.next().await {
	println!("Got a message!");
}
```
### Writing
```rust
let _ = stream.send(...).await;
```
