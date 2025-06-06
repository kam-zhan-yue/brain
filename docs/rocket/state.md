Many web applications have a need to maintain state. Rocket provides this through a *managed state*. The state is managed on a per-type basis: Rocket will manage at most one value of a given type.

The process for using managed state is simple:
1. Call `manage` on the `Rocket` instance corresponding to your application with the initial value of the state.
2. Add a `State<T>` to any request handler, where `T` is the type of the value passed into `manage`.

```rust

#[launch]
fn rocket() -> _ {
    let game: Game = Game::default();

    rocket::build()
        .mount("/", routes![index, echo_stream, game_stream])
        .manage(game)
}


```
