## Concurrency
---
Concurrency and parallelism are not the same thing. If you alternate between two tasks, then you are working on tasks concurrently, but not in parallel. For it to qualify as parallel, you need two people, one dedicated to each task.

Tokio's asynchronous code allows you to work on many tasks concurrently, without having to wok on them in parallel using ordinary threads. Tokio can run many tasks concurrently on a single thread.

## Spawning Tasks
---
To process connections concurrently, a new task is spawned for each inbound connection. The connection is processed on this task.

A Tokio task is an asynchronous green thread. They are created by passing an `async` block to `tokio::spawn`. The `tokio::spawn` function returns a `JoinHandle`, which the caller can use to interact with the spawned task. The `async` lock may have a return value that can be obtained using `.await` (this returns a `Result)
```rust
#[tokio::main]
async fn main() { 
	let handle = tokio::spawn(async {
		 // Do some async work 
		 "return value" 
	 }); 
	 // Do some other work 
	 let out = handle.await.unwrap();
	  println!("GOT {}", out);
}
```

Tasks in Tokio are lightweight, only requiring a single allocation and 64 bytes of memory. Applications should spawn thousands, if not millions of tasks.
### `'static` bound
When you spawn a task on the Tokio runtime, its type's lifetime must be `'static`. This means that the spawned task must not contain any references to data owned outside of the task.

> It is a misconception that `'static` always means that the task 'lives forever', but this is not the case. Just because a value is `'static` does not mean there is a memory leak.

The following will not compile because `v` is owned by the function outside of the task. In order for it to compile, `v` must be moved into the task.

```rust
use tokio::task;
#[tokio::main]
async fn main() {
	let v = vec![1, 2, 3];
	task::spawn(async {
		println!("Here's a vec: {:?}", v);
	});
}
```

If a single piece of data must be accessible for more than one task concurrently, then it must be shared using synchronisation primitives such as `Arc`