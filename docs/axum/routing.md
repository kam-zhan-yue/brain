Routing in `axum` is setup as handler functions that are routed by `axum::Router`.

## Routing
`Router` is used to set up which paths go to which services.

```rust
use axum::{Router, routing::get};

// our router
let app = Router::new()
    .route("/", get(root))
    .route("/foo", get(get_foo).post(post_foo))
    .route("/foo/bar", get(foo_bar));

// which calls one of these handlers
async fn root() {}
async fn get_foo() {}
async fn post_foo() {}
async fn foo_bar() {}
```

## `merge`

Merge the paths and fallbacks of two routers into a single `Router`. This is useful for breaking apps into smaller pieces and combining them into one.

```rust
use axum::{
    routing::get,
    Router,
};

// define some routes separately
let user_routes = Router::new()
    .route("/users", get(users_list))
    .route("/users/{id}", get(users_show));

let team_routes = Router::new()
    .route("/teams", get(teams_list));

// combine them into one
let app = Router::new()
    .merge(user_routes)
    .merge(team_routes);

// could also do `user_routes.merge(team_routes)`

// Our app now accepts
// - GET /users
// - GET /users/{id}
// - GET /teams
```

### Merging Routers with State

When combining `Router`s with this method, each `Router` must have the same type of state. If your routers have different types, you can use `Router::with_state` to provide the state and make them match.

```rust
use axum::{
    Router,
    routing::get,
    extract::State,
};

#[derive(Clone)]
struct InnerState {}

#[derive(Clone)]
struct OuterState {}

async fn inner_handler(state: State<InnerState>) {}

let inner_router = Router::new()
    .route("/bar", get(inner_handler))
    .with_state(InnerState {});

async fn outer_handler(state: State<OuterState>) {}

let app = Router::new()
    .route("/", get(outer_handler))
    .merge(inner_router)
    .with_state(OuterState {});
```

## `nest`

Nest a `Router` at some path.

```rust
use axum::{
    routing::{get, post},
    Router,
};

let user_routes = Router::new().route("/{id}", get(|| async {}));

let team_routes = Router::new().route("/", post(|| async {}));

let api_routes = Router::new()
    .nest("/users", user_routes)
    .nest("/teams", team_routes);

let app = Router::new().nest("/api", api_routes);

// Our app now accepts
// - GET /api/users/{id}
// - POST /api/teams
```
## `with_state`

Provides the state for the router. State passed into this method is global and will be used for all requests that the router receives. It is not received for holding state derived form a request.

```rust
use axum::{Router, routing::get, extract::State};

#[derive(Clone)]
struct AppState {}

let routes = Router::new()
    .route("/", get(|State(state): State<AppState>| async {
        // use state
    }))
    .with_state(AppState {});

let listener = tokio::net::TcpListener::bind("0.0.0.0:3000").await.unwrap();
axum::serve(listener, routes).await.unwrap();
```

It is recommended to not stet the state directly. Instead, do it before you run the server.
```rust

use axum::{Router, routing::get, extract::State};

#[derive(Clone)]
struct AppState {}

// Don't call `Router::with_state` here
fn routes() -> Router<AppState> {
    Router::new()
        .route("/", get(|_: State<AppState>| async {}))
}

// Instead do it before you run the server
let routes = routes().with_state(AppState {});

let listener = tokio::net::TcpListener::bind("0.0.0.0:3000").await.unwrap();
axum::serve(listener, routes).await.unwrap();
```

This is because we can only call `Router::into_make_service` on `Router<()>` and not `Router<AppState>`. The state defaults to `Router<()>`.

### What `S` in `Router<S>` means
`Router<S>` means a router is *missing* a state of type `S` to be able to handle requests. It does not mean a `Router` that has a state of type `S`

```rust
// A router that _needs_ an `AppState` to handle requests
let router: Router<AppState> = Router::new()
    .route("/", get(|_: State<AppState>| async {}));

// Once we call `Router::with_state` the router isn't missing
// the state anymore, because we just provided it
//
// Therefore the router type becomes `Router<()>`, i.e a router
// that is not missing any state
let router: Router<()> = router.with_state(AppState {});

// Only `Router<()>` has the `into_make_service` method.
//
// You cannot call `into_make_service` on a `Router<AppState>`
// because it is still missing an `AppState`.
let listener = tokio::net::TcpListener::bind("0.0.0.0:3000").await.unwrap();
axum::serve(listener, router).await.unwrap();
```