## Basic Types

Anything that implements `IntoResponse` can be returned from a handler. `axum` provides implementations for common types:
```rust
// `()` gives an empty response
async fn empty() {}

// String will get a text/plain; charset=utf-8 content-type
async fn plain_text(uri: Uri) -> String {
	format!("Hi from {}", uri.path())
}

// `Json` will get a `application/json` content-type and work with anything that implements `serde::Serialize`
async fn json() -> Json<Vec<String>> {
	Json(vec!["foo".to_owned(), "bar".to_owned()])
}

// `Html` will get a `text/html`. content-type
async fn html() -> Html<&'static str> {
	Html("<p>Hello, World!</p>")
}
```

## Tuples

We can also return tuples to build more complex responses

```rust
async fn with_status(uri: Uri) -> (StatusCode, String) {
	(StatusCode::NOT_FOUND, format!("Not Found: {}", uri.path()))
}

// Use `impl IntoResponse` to avoid having to type the whole type
async fn impl_trait(uri: Uri) -> impl IntoResponse {
	(StatusCode::NOT_FOUND, format!("Not Found: {}", uri.path()))
}
```

## `Response`
We can also use. `Response` for more low level control

```rust
async fn response() -> Response {
	Response::builder()
		.status(StatusCode::NOT_FOUND)
		.header("x-foo", "custom-header")
		.body(Body::from("not found"))
		.unwrap()
}
```

## Different Response Types

If you need to return multiple response types, you can call `into_response()`

```rust
async fn handle() -> Response {
	if something() {
		"All good!".into_response()
	} else if something_else() {
		(
			StatusCode::INTERNAL_SERVER_ERROR, 
			"Something went wrong..."
		).into_response()
	} else {
		Redirect::to("/").into_response()
	}
}
```