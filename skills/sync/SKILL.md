---
name: rust-std-sync
description: |
  CRITICAL: Use for Rust std sync primitives. Triggers on:
  Arc, Mutex, RwLock, Atomic, AtomicBool, AtomicUsize, mpsc, channel,
  Condvar, Barrier, Once, OnceLock, LazyLock, Send, Sync,
  并发, 锁, 原子, 线程安全, 通道, 互斥
---

# Rust Standard Sync Primitives

> **Version:** 1.92.0 | **Last Updated:** 2026-01-19
>
> Source: https://doc.rust-lang.org/std/sync/

Help users with Rust standard library synchronization primitives.

## Documentation

Refer to the local files for detailed documentation:
- `../../references/sync/mutex-arc.md` - Mutex and Arc patterns
- `../../references/sync/atomics.md` - Atomic types usage
- `../../references/sync/thread.md` - Thread creation and management
- `../../references/sync/mpsc.md` - Channel communication
- `../../references/_shared/rust-defaults.md` - Rust code generation defaults

## Sync Primitives Overview

| Type | Purpose | Blocking |
|------|---------|----------|
| `Mutex<T>` | Mutual exclusion | Yes |
| `RwLock<T>` | Read-write lock | Yes |
| `Arc<T>` | Thread-safe Rc | No |
| `Atomic*` | Lock-free primitives | No |
| `Condvar` | Condition variable | Yes |
| `Barrier` | Synchronization point | Yes |
| `Once`/`OnceLock` | One-time initialization | Yes |
| `LazyLock` | Lazy initialization | Yes |
| `mpsc` | Message passing | Yes (recv) |

## Key Patterns

### Arc + Mutex (Shared Mutable State)
```rust
use std::sync::{Arc, Mutex};
use std::thread;

let data = Arc::new(Mutex::new(0));

let handles: Vec<_> = (0..10).map(|_| {
    let data = Arc::clone(&data);
    thread::spawn(move || {
        let mut guard = data.lock().unwrap();
        *guard += 1;
    })
}).collect();

for h in handles {
    h.join().unwrap();
}

println!("Result: {}", *data.lock().unwrap());
```

### RwLock (Multiple Readers, Single Writer)
```rust
use std::sync::{Arc, RwLock};

let data = Arc::new(RwLock::new(vec![1, 2, 3]));

// Multiple readers OK
let read_guard = data.read().unwrap();
println!("{:?}", *read_guard);
drop(read_guard);

// Single writer
let mut write_guard = data.write().unwrap();
write_guard.push(4);
```

### Atomic Types
```rust
use std::sync::atomic::{AtomicUsize, Ordering};

let counter = AtomicUsize::new(0);

// Increment
counter.fetch_add(1, Ordering::SeqCst);

// Compare and swap
counter.compare_exchange(1, 2, Ordering::SeqCst, Ordering::SeqCst);

// Load/Store
let val = counter.load(Ordering::SeqCst);
counter.store(10, Ordering::SeqCst);
```

### mpsc Channel (Message Passing)
```rust
use std::sync::mpsc;
use std::thread;

let (tx, rx) = mpsc::channel();

// Clone sender for multiple producers
let tx2 = tx.clone();

thread::spawn(move || {
    tx.send("from thread 1").unwrap();
});

thread::spawn(move || {
    tx2.send("from thread 2").unwrap();
});

// Receive messages
for msg in rx {
    println!("{}", msg);
}
```

### OnceLock (One-Time Init, Rust 1.70+)
```rust
use std::sync::OnceLock;

static CONFIG: OnceLock<String> = OnceLock::new();

fn get_config() -> &'static str {
    CONFIG.get_or_init(|| {
        "initialized".to_string()
    })
}
```

### LazyLock (Lazy Init, Rust 1.80+)
```rust
use std::sync::LazyLock;

static GLOBAL: LazyLock<Vec<i32>> = LazyLock::new(|| {
    vec![1, 2, 3]
});
```

## Memory Ordering

| Ordering | Description | Use Case |
|----------|-------------|----------|
| `Relaxed` | No ordering guarantee | Counters |
| `Acquire` | Reads see prior writes | Load after lock |
| `Release` | Writes visible to acquire | Store before unlock |
| `AcqRel` | Both acquire and release | Read-modify-write |
| `SeqCst` | Strongest, sequential | Default safe choice |

## API Reference Table

| Type | Lock/Access | Unlock/Release |
|------|-------------|----------------|
| `Mutex<T>` | `lock()` → `MutexGuard` | Drop guard |
| `RwLock<T>` | `read()` / `write()` | Drop guard |
| `Arc<T>` | `clone()` to share | Drop to decrement |
| `AtomicUsize` | `load()` / `store()` | N/A |

## Send and Sync Traits

| Trait | Meaning |
|-------|---------|
| `Send` | Safe to transfer ownership to another thread |
| `Sync` | Safe to share references between threads |

| Type | Send | Sync |
|------|------|------|
| `i32`, `String` | Yes | Yes |
| `Rc<T>` | No | No |
| `Arc<T>` | Yes (if T: Send+Sync) | Yes (if T: Send+Sync) |
| `Mutex<T>` | Yes (if T: Send) | Yes (if T: Send) |
| `Cell<T>` | Yes (if T: Send) | No |
| `RefCell<T>` | Yes (if T: Send) | No |

## When Writing Code

1. Prefer `Arc<Mutex<T>>` for shared mutable state
2. Use `RwLock` when reads >> writes
3. Use `Atomic*` for simple counters/flags
4. Use `mpsc` for message passing between threads
5. Use `OnceLock`/`LazyLock` instead of `lazy_static!`
6. Always handle `PoisonError` from lock failures
7. Keep lock scope minimal - drop guards early

## When Answering Questions

1. Explain Send/Sync implications
2. Mention deadlock risks with multiple locks
3. Suggest appropriate primitive for use case
4. Reference parking_lot for performance-critical code
