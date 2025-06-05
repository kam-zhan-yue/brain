Tokio is an asynchronous runtime for Rust. It provides the building blocks needed for writing networking applications. At a high level, Tokio provides the following major components
- A multi-threaded runtime for executing asynchronous code
- An asynchronous version of the standard library
- A large ecosystem of libraries

### Why is it needed?
---
Asynchronous Rust code does not run on its own, so you need to use a runtime to execute it. The Tokio runtime is the most widely used runtime and provides many utilities.
### When not to use it?
---
- Speeding up CPU-bound computations by running them in parallel on several threads. Tokio is designed for IO-bound applications where each individual task spends most of its time waiting for IO.
- Reading a lot of files. This is because the operating system does not provide asynchronous file APIs.
- Sending a single web request. Tokio is good when you need to do many things at the same time.
