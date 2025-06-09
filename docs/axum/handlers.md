A handler is an async function that accepts zero or more "extractors" as arguments and returns something that can be converted into a response. Handlers are where the application logic lives and `axum` applications are built by routing between handlers.

Examples of handlers are
```rust
use axum::{body::Bytes, http::StatusCode};

// Handler that immediately returns an empty `200 OK` response.
async fn unit_handler() {}

// Handler that immediately returns a `200 OK` response with a plain text
// body.
async fn string_handler() -> String {
    "Hello, World!".to_string()
}

// Handler that buffers the request body and returns it.
//
// This works because `Bytes` implements `FromRequest`
// and therefore can be used as an extractor.
//
// `String` and `StatusCode` both implement `IntoResponse` and
// therefore `Result<String, StatusCode>` also implements `IntoResponse`
async fn echo(body: Bytes) -> Result<String, StatusCode> {
    if let Ok(string) = String::from_utf8(body.to_vec()) {
        Ok(string)
    } else {
        Err(StatusCode::BAD_REQUEST)
    }
}
```