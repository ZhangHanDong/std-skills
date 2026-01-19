# Mutex and Arc Reference

## Arc (Atomic Reference Counting)

Thread-safe version of `Rc`.

```rust
use std::sync::Arc;

let data = Arc::new(vec![1, 2, 3]);

// Clone to share (increments ref count)
let data2 = Arc::clone(&data);
// or: let data2 = data.clone();

// Access
println!("{:?}", *data);

// Get strong count
println!("refs: {}", Arc::strong_count(&data));
```

## Mutex (Mutual Exclusion)

```rust
use std::sync::Mutex;

let mutex = Mutex::new(5);

// Lock and access
{
    let mut guard = mutex.lock().unwrap();
    *guard += 1;
}  // Lock released when guard is dropped

// Try lock (non-blocking)
if let Ok(mut guard) = mutex.try_lock() {
    *guard += 1;
}
```

## Arc + Mutex Pattern

```rust
use std::sync::{Arc, Mutex};
use std::thread;

let counter = Arc::new(Mutex::new(0));
let mut handles = vec![];

for _ in 0..10 {
    let counter = Arc::clone(&counter);
    let handle = thread::spawn(move || {
        let mut num = counter.lock().unwrap();
        *num += 1;
    });
    handles.push(handle);
}

for handle in handles {
    handle.join().unwrap();
}

println!("Result: {}", *counter.lock().unwrap());
```

## Handling Poison

A mutex becomes "poisoned" if a thread panics while holding the lock.

```rust
use std::sync::Mutex;

let mutex = Mutex::new(0);

match mutex.lock() {
    Ok(guard) => {
        // Use guard
    }
    Err(poisoned) => {
        // Recover the guard anyway
        let guard = poisoned.into_inner();
    }
}

// Or just unwrap (propagate panic)
let guard = mutex.lock().unwrap();
```

## RwLock (Multiple Readers)

```rust
use std::sync::RwLock;

let lock = RwLock::new(5);

// Multiple readers OK
{
    let r1 = lock.read().unwrap();
    let r2 = lock.read().unwrap();
    println!("{}, {}", *r1, *r2);
}

// Single writer
{
    let mut w = lock.write().unwrap();
    *w += 1;
}
```

## Best Practices

1. Keep lock scope minimal
2. Don't hold locks across await points (use tokio::sync::Mutex)
3. Watch for deadlock with multiple locks
4. Consider parking_lot for better performance
5. Use RwLock when reads >> writes
