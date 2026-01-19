# Atomic Types Reference

## Available Types

| Type | Size | Notes |
|------|------|-------|
| `AtomicBool` | 1 byte | Boolean |
| `AtomicI8`/`AtomicU8` | 1 byte | 8-bit integer |
| `AtomicI16`/`AtomicU16` | 2 bytes | 16-bit integer |
| `AtomicI32`/`AtomicU32` | 4 bytes | 32-bit integer |
| `AtomicI64`/`AtomicU64` | 8 bytes | 64-bit integer |
| `AtomicIsize`/`AtomicUsize` | pointer size | Platform-dependent |
| `AtomicPtr<T>` | pointer size | Raw pointer |

## Common Operations

```rust
use std::sync::atomic::{AtomicUsize, Ordering};

let counter = AtomicUsize::new(0);

// Load (read)
let val = counter.load(Ordering::SeqCst);

// Store (write)
counter.store(10, Ordering::SeqCst);

// Swap (exchange)
let old = counter.swap(20, Ordering::SeqCst);

// Fetch and add/sub
let old = counter.fetch_add(1, Ordering::SeqCst);
let old = counter.fetch_sub(1, Ordering::SeqCst);

// Compare and exchange
let result = counter.compare_exchange(
    20,                    // expected
    30,                    // new value
    Ordering::SeqCst,      // success ordering
    Ordering::SeqCst,      // failure ordering
);
```

## Memory Ordering

| Ordering | Guarantee | Performance |
|----------|-----------|-------------|
| `Relaxed` | None (only atomicity) | Fastest |
| `Acquire` | Loads after see prior stores | Fast |
| `Release` | Stores before visible to acquire | Fast |
| `AcqRel` | Both acquire and release | Medium |
| `SeqCst` | Total global order | Slowest |

### When to Use

- **Relaxed**: Simple counters where exact order doesn't matter
- **Acquire**: Loading a flag that signals data is ready
- **Release**: Storing a flag that signals data is ready
- **AcqRel**: Read-modify-write operations
- **SeqCst**: When in doubt (safest default)

## AtomicBool Example

```rust
use std::sync::atomic::{AtomicBool, Ordering};
use std::sync::Arc;
use std::thread;

let running = Arc::new(AtomicBool::new(true));
let r = Arc::clone(&running);

let handle = thread::spawn(move || {
    while r.load(Ordering::Relaxed) {
        // Do work
    }
});

// Signal stop
running.store(false, Ordering::Relaxed);
handle.join().unwrap();
```

## AtomicPtr Example

```rust
use std::sync::atomic::{AtomicPtr, Ordering};
use std::ptr;

let ptr = AtomicPtr::new(ptr::null_mut::<i32>());

let mut value = 42;
ptr.store(&mut value, Ordering::SeqCst);

let loaded = ptr.load(Ordering::SeqCst);
unsafe {
    if !loaded.is_null() {
        println!("{}", *loaded);
    }
}
```

## Best Practices

1. Start with `SeqCst` until you understand the requirements
2. Use `Relaxed` only for simple counters with no synchronization needs
3. Pair `Release` stores with `Acquire` loads
4. Prefer higher-level primitives (Mutex, channels) when possible
5. Document why specific orderings are correct
