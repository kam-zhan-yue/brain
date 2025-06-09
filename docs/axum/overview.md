Axum is a web application framework that focuses on ergonomics and modularity.
- Route requests to handlers with a macro-free API
- Declaratively parse requests using extractors
- Simply and predictable error handling model
- Generate responses with minimal boilerplate
- Take advantage of the `tower` and `tower-http` ecosystems of middleware, services, and utilities

`axum` doesn't have its own middleware system but instead uses `tower::Service`.

`axum` is designed to work with `tokio` and `hyper`.
