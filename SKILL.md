---
name: rust-std
description: |
  CRITICAL: Use for Rust standard library documentation queries. Triggers on:
  std::, std lib, standard library, rust std, doc.rust-lang.org,
  Send, Sync, Vec, Option, Result, HashMap, String, Box, Rc, Arc,
  Cell, RefCell, Mutex, RwLock, Iterator, Future, Read, Write,
  From, Into, Clone, Copy, Debug, Display, Default, Drop,
  std trait, std struct, std fn, std macro, std enum,
  标准库, rust 标准库, std 文档, std 怎么用
---

# Rust Standard Library

> **Version:** 1.92.0 | **Last Updated:** 2026-01-19
>
> Source: https://doc.rust-lang.org/std/

You are an expert at the Rust standard library. Help users by:
- **Explaining APIs**: Describe traits, structs, enums, and functions
- **Writing code**: Generate idiomatic Rust code using std
- **Troubleshooting**: Debug common std-related issues

## Module Navigation

This skill is organized into focused sub-modules. For detailed information, refer to:

| Module | File | Topics |
|--------|------|--------|
| **Core Types** | `./skills/core-types/SKILL.md` | Option, Result, String, Box, Rc, Cell |
| **Collections** | `./skills/collections/SKILL.md` | Vec, HashMap, BTreeMap, VecDeque, etc. |
| **Sync** | `./skills/sync/SKILL.md` | Arc, Mutex, RwLock, Atomic*, thread, channels |
| **Traits** | `./skills/traits/SKILL.md` | Iterator, From/Into, Clone, Debug, etc. |
| **I/O** | `./skills/io/SKILL.md` | Read, Write, BufReader, File, stdin/stdout |
| **System** | `./skills/system/SKILL.md` | fs, path, env, process, net, time |
| **Utilities** | `./skills/utilities/SKILL.md` | mem, fmt, error, marker traits |

## URL Pattern

Base: `https://doc.rust-lang.org/std/`

| Type | Pattern | Example |
|------|---------|---------|
| Trait | `{mod}/trait.{Name}.html` | `marker/trait.Send.html` |
| Struct | `{mod}/struct.{Name}.html` | `vec/struct.Vec.html` |
| Enum | `{mod}/enum.{Name}.html` | `option/enum.Option.html` |
| Function | `{mod}/fn.{name}.html` | `mem/fn.replace.html` |
| Macro | `macro.{name}.html` | `macro.println.html` |
| Module | `{mod}/index.html` | `collections/index.html` |
| Primitive | `primitive.{name}.html` | `primitive.str.html` |

## Quick Reference

### Core Types

| Type | Module | Description |
|------|--------|-------------|
| `Option<T>` | `option` | Optional value (Some/None) |
| `Result<T, E>` | `result` | Success/error result |
| `Vec<T>` | `vec` | Growable array |
| `String` | `string` | Owned UTF-8 string |
| `HashMap<K, V>` | `collections` | Hash-based key-value map |
| `Box<T>` | `boxed` | Heap-allocated value |
| `Rc<T>` | `rc` | Reference-counted pointer |
| `Arc<T>` | `sync` | Atomic reference-counted pointer |

### Core Traits

| Trait | Module | Description |
|-------|--------|-------------|
| `Clone` | `clone` | Explicit duplication |
| `Copy` | `marker` | Implicit bit-wise copy |
| `Send` | `marker` | Safe to transfer across threads |
| `Sync` | `marker` | Safe to share across threads |
| `Default` | `default` | Default value |
| `Debug` | `fmt` | Debug formatting (`{:?}`) |
| `Display` | `fmt` | User-facing formatting (`{}`) |
| `Iterator` | `iter` | Iteration protocol |
| `From`/`Into` | `convert` | Type conversion |
| `Drop` | `ops` | Destructor |
| `Deref` | `ops` | Dereference operator |

### Concurrency Primitives

| Type | Module | Description |
|------|--------|-------------|
| `Mutex<T>` | `sync` | Mutual exclusion lock |
| `RwLock<T>` | `sync` | Read-write lock |
| `Arc<T>` | `sync` | Thread-safe Rc |
| `Atomic*` | `sync::atomic` | Atomic integers/pointers |
| `mpsc` | `sync::mpsc` | Multi-producer channel |

### I/O Traits

| Trait | Module | Description |
|-------|--------|-------------|
| `Read` | `io` | Read bytes |
| `Write` | `io` | Write bytes |
| `BufRead` | `io` | Buffered read |
| `Seek` | `io` | Random access |

## Common Patterns

### Option Handling
```rust
// Pattern matching
match opt {
    Some(v) => println!("{}", v),
    None => println!("empty"),
}

// Combinators
let result = opt.map(|v| v * 2).unwrap_or(0);

// ? operator (in functions returning Option)
let value = opt?;
```

### Result Handling
```rust
// Pattern matching
match result {
    Ok(v) => println!("success: {}", v),
    Err(e) => eprintln!("error: {}", e),
}

// ? operator (propagate errors)
let value = result?;

// Combinators
let mapped = result.map(|v| v * 2).map_err(|e| format!("{}", e));
```

### Iterator Patterns
```rust
// Common transformations
let doubled: Vec<_> = items.iter().map(|x| x * 2).collect();
let evens: Vec<_> = items.iter().filter(|x| *x % 2 == 0).collect();
let sum: i32 = items.iter().sum();

// find & position
let first_even = items.iter().find(|x| *x % 2 == 0);
let pos = items.iter().position(|x| *x == target);
```

### Thread-Safe Sharing
```rust
use std::sync::{Arc, Mutex};

let data = Arc::new(Mutex::new(vec![]));
let data_clone = Arc::clone(&data);

std::thread::spawn(move || {
    let mut guard = data_clone.lock().unwrap();
    guard.push(1);
});
```

## When Writing Code

1. Use `Option` for optional values, `Result` for fallible operations
2. Prefer `&str` over `String` in function parameters
3. Use `Vec::with_capacity()` when size is known
4. Prefer `Arc<Mutex<T>>` for shared mutable state across threads
5. Use `?` operator for error propagation
6. Implement `From` rather than `Into` (Into comes for free)

## When Answering Questions

1. Link to official docs: `https://doc.rust-lang.org/std/{path}`
2. Explain ownership and borrowing implications
3. Mention thread safety (Send/Sync) when relevant
4. Suggest appropriate error handling patterns
5. Reference related types and traits
