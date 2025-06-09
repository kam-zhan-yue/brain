`pub struct Mutex<T: ?Sized>`

A mutual exclusion primitive useful for protecting shared data. A mutex will block threads waiting for the lock to become available. The mutex can be created with a `new` constructor.

The data can only be accessed through the RAII guards returned from `lock` and `try_lock`. This guarantees that the data is only ever accessed when the mutex is locked.

### Poisoning
A mutex is considered poisoned whenever a thread panics while holding the mutex. Once a mutex is poisoned, all other threads are unable to access the data by default as it is likely tainted.

Hence, `lock` and `try_lock` return a `Result` which indicates if the mutex has been poisoned or not.

```rust
use std::sync::{Arc, Mutex};
use std::thread;
use std::sync::mpsc::channel;

const N: usize = 10;

// Spawn a few threads to increment a shared variable (non-atomically), and
// let the main thread know once all increments are done.
//
// Here we're using an Arc to share memory among threads, and the data inside
// the Arc is protected with a mutex.
let data = Arc::new(Mutex::new(0));

let (tx, rx) = channel();
for _ in 0..N {
    let (data, tx) = (Arc::clone(&data), tx.clone());
    thread::spawn(move || {
        // The shared state can only be accessed once the lock is held.
        // Our non-atomic increment is safe because we're the only thread
        // which can access the shared state when the lock is held.
        //
        // We unwrap() the return value to assert that we are not expecting
        // threads to ever fail while holding the lock.
        let mut data = data.lock().unwrap();
        *data += 1;
        if *data == N {
            tx.send(()).unwrap();
        }
        // the lock is unlocked here when `data` goes out of scope.
    });
}

rx.recv().unwrap();
```