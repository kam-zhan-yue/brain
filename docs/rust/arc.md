A thread-safe reference-counting pointer. `Arc<T>` provides shared ownership of a value of type `T`, allocated in the heap.

Invoking `clone` on `Arc` produces a new `Arc` instance, which points to the same allocation on the heap as the source `Arc`