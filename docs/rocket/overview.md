Rocket provides primitives to build web servers and applications in Rust. Rocket provides routing, pre-processing of requests, and post-processing of responses. Your application code instructs Rocket on what to pre-process and post-process and fills the gaps between pre-processing and post-processing.

## Lifecycle

Rocket’s main task is to listen for incoming web requests, dispatch the request to the application code, and return a response to the client. The lifecycle refers to the process that goes from request to response. The lifecycle is summarised as the following:

1. Routing: Rocket parses an incoming HTTP request into native structures that your code operates on indirectly. Rocket determines which request handler to invoke by matching against route attributes declared in your application.
2. Validation: Rocket validates the incoming request against types and guards present in the matched route. If validation fails, Rocket _forwards_ the request to the next matching route or calls an _error handler_.
3. Processing: The request handler associated with the route is invoked with validated arguments. This is the main business logic of an application. Processing completes by returning a `Response`.
4. Response: The returned `Response` is processed. Rocket generates the appropriate HTTP response and sends it to the client. This completes the lifecycle. Rocket continues listening for requests, restarting the lifecycle for each incoming request.

## Routing
---
Rocket applications are centred around routes and handlers. A _route_ is a combination of:

- A set of parameters to match an incoming request against
- A handler to process the request and return a response

A _handler_ is simply a function that takes an arbitrary number of arguments and returns any arbitrary type. The parameters to match against include static paths, dynamic paths, path segments, forms, query strings, request format specifiers, and body data.

Rocket uses attributes, which look like function decorators in other languages, to make declaring routes easy. Routes are declared by annotating a function, the handler, with the set of parameters to match against. A complete route declaration looks like:

```rust
#[get("/world")]              // <- route attribute
fn world() -> &'static str {  // <- request handler
    "hello, world!"
}
```

- This declares the `world` route to match against the static path `"/world"` on incoming `GET` requests. Instead of `#[get]`, we could have used `#[post]` or `#[put]`

## Mounting
---
Before Rocket can dispatch requests to a route, the route needs to be _mounted_.

```rust
rocket::build().mount("/hello", routes![world]);
```

The `mount` method takes an input:

1. A _base_ path to the namespace a list of routes under
2. A list of routes via the `routes!` macro

This creates a new `Rocket` instance via the `build` function and mounts the `world` route to the `/hello` base path, making Rocket aware of the route. `GET` requests to the `/hello/world` will be directed to the `world` function

```rust
rocket::build()
    .mount("/hello", routes![world])
    .mount("/hi", routes![world]);
```

We can mount `world` to many base paths.

## Launching
---
Rocket begins serving requests after being _launched_, which starts a multi-threaded asynchronous server and dispatches requests to matching routes as they arrive.

There are two mechanisms by which a `Rocket` can be launched. The first and preferred approach is through the `#[launch]` route attribute, which generates a `main` function that sets up an async runtime and starts the server. With `#[launch`], our complete _Hello, world!_ application looks like:

```rust
#[macro_use] extern crate rocket;

#[get("/world")]
fn world() -> &'static str {
    "Hello, world!"
}

#[launch]
fn rocket() -> _ {
    rocket::build().mount("/hello", routes![world])
}o
```

<aside> 💡
The return type of a function decorated with `#[launch]` is automatically inferred when the return type is set to `_`. If you prefer, you can set the return type explicitly to `Rocket<Build>`
</aside>

The second approach uses the `#[rocket::main]` route attribute. This also generates a main function that sets up an async runtime. But it allows us to start the server.

This is useful when a handle to the `Future` returned by `launch()` is desired, or when the return value of `launch()` is to be inspected.