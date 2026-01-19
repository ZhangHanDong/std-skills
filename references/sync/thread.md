# Thread (std::thread) Reference

## Spawning Threads

### Basic spawn
```rust
use std::thread;

let handle = thread::spawn(|| {
    println!("Hello from thread!");
    42  // Return value
});

let result = handle.join().unwrap();  // Wait and get result
```

### With move closure
```rust
let data = vec![1, 2, 3];

let handle = thread::spawn(move || {
    // data is moved into thread
    println!("{:?}", data);
});

handle.join().unwrap();
```

## JoinHandle

```rust
use std::thread::{self, JoinHandle};

let handle: JoinHandle<i32> = thread::spawn(|| 42);

// join() returns Result<T, Box<dyn Any>>
match handle.join() {
    Ok(value) => println!("Thread returned: {}", value),
    Err(e) => println!("Thread panicked: {:?}", e),
}
```

## Thread Builder

```rust
use std::thread;

let handle = thread::Builder::new()
    .name("worker-1".to_string())
    .stack_size(4 * 1024 * 1024)  // 4 MB stack
    .spawn(|| {
        println!("Thread name: {:?}", thread::current().name());
    })?;
```

## Scoped Threads (Rust 1.63+)

Borrow data without moving:

```rust
use std::thread;

let data = vec![1, 2, 3];

thread::scope(|s| {
    s.spawn(|| {
        // Can borrow data without move!
        println!("{:?}", data);
    });

    s.spawn(|| {
        println!("len = {}", data.len());
    });
});  // All threads joined automatically

// data is still usable here
println!("{:?}", data);
```

## Thread-Local Storage

```rust
use std::cell::RefCell;
use std::thread;

thread_local! {
    static COUNTER: RefCell<u32> = RefCell::new(0);
}

COUNTER.with(|c| {
    *c.borrow_mut() += 1;
});

// Each thread has its own copy
thread::spawn(|| {
    COUNTER.with(|c| {
        println!("Thread value: {}", *c.borrow());  // 0, not shared
    });
}).join().unwrap();
```

## Common Functions

| Function | Description |
|----------|-------------|
| `spawn(f)` | Create new thread |
| `current()` | Get current Thread handle |
| `sleep(dur)` | Sleep for duration |
| `sleep_until(instant)` | Sleep until instant (1.77+) |
| `yield_now()` | Yield to scheduler |
| `park()` | Block until unparked |
| `scope(f)` | Create scoped threads |
| `panicking()` | Check if unwinding |
| `available_parallelism()` | Get CPU count |

## Thread Handle Methods

```rust
let current = thread::current();

current.name()   // Option<&str>
current.id()     // ThreadId
current.unpark() // Wake parked thread
```

## Sleep and Yield

```rust
use std::thread;
use std::time::Duration;

// Sleep
thread::sleep(Duration::from_millis(100));

// Yield (hint to scheduler)
thread::yield_now();
```

## Park/Unpark

```rust
use std::thread;
use std::sync::Arc;
use std::time::Duration;

let t = thread::current();
let t_clone = t.clone();

thread::spawn(move || {
    thread::sleep(Duration::from_secs(1));
    t_clone.unpark();  // Wake main thread
});

thread::park();  // Block until unparked
```

## CPU Parallelism

```rust
use std::thread;

let cpus = thread::available_parallelism()?.get();
println!("Available CPUs: {}", cpus);
```

## Common Patterns

### Thread Pool (Manual)
```rust
use std::thread;
use std::sync::mpsc;

let (tx, rx) = mpsc::channel();

for _ in 0..4 {
    let rx = rx.clone();  // Won't compile - need Arc<Mutex<>>
}

// Use crossbeam or rayon for real thread pools
```

### Worker Pattern
```rust
use std::thread;
use std::sync::mpsc;

let (tx, rx) = mpsc::channel::<Box<dyn FnOnce() + Send>>();

let handle = thread::spawn(move || {
    while let Ok(job) = rx.recv() {
        job();
    }
});

tx.send(Box::new(|| println!("Job 1"))).unwrap();
tx.send(Box::new(|| println!("Job 2"))).unwrap();
drop(tx);  // Signal shutdown
handle.join().unwrap();
```
