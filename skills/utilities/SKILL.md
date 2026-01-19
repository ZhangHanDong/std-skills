---
name: rust-std-utilities
description: |
  CRITICAL: Use for Rust utility modules. Triggers on:
  std::mem, std::fmt, std::error, std::marker,
  size_of, align_of, swap, replace, take, drop, forget, transmute,
  Display, Debug, format!, write!, Formatter,
  Error trait, source, thiserror, anyhow,
  Send, Sync, Copy, Sized, PhantomData, Unpin,
  内存操作, 格式化, 错误处理, 标记 trait
---

# Rust Utility Modules

> **Version:** 1.92.0 | **Last Updated:** 2026-01-19
>
> Source: https://doc.rust-lang.org/std/

Help users with memory operations, formatting, error handling, and marker traits.

## Documentation

Refer to the local files for detailed documentation:
- `../../references/utilities/mem.md` - Memory operations
- `../../references/utilities/fmt.md` - Formatting and Display/Debug
- `../../references/utilities/error.md` - Error trait
- `../../references/utilities/marker.md` - Send, Sync, Copy, etc.
- `../../references/_shared/rust-defaults.md` - Rust code generation defaults

## Module Overview

| Module | Key Items | Purpose |
|--------|-----------|---------|
| `std::mem` | size_of, swap, replace, take | Memory manipulation |
| `std::fmt` | Display, Debug, Formatter | Text formatting |
| `std::error` | Error trait | Error handling |
| `std::marker` | Send, Sync, Copy, Sized | Type markers |

## Key Patterns

### Memory Operations
```rust
use std::mem;

// Swap values
mem::swap(&mut a, &mut b);

// Replace and get old value
let old = mem::replace(&mut v, new_value);

// Take and replace with default
let taken = mem::take(&mut option_value);

// Size and alignment
let size = mem::size_of::<MyStruct>();
let align = mem::align_of::<MyStruct>();
```

### Formatting
```rust
use std::fmt;

// Implement Display
impl fmt::Display for MyType {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "{}", self.value)
    }
}

// Format specifiers
format!("{}", val)      // Display
format!("{:?}", val)    // Debug
format!("{:#?}", val)   // Pretty Debug
format!("{:x}", num)    // Hex
format!("{:.2}", float) // Precision
```

### Error Handling
```rust
use std::error::Error;
use std::fmt;

#[derive(Debug)]
struct MyError { msg: String }

impl fmt::Display for MyError {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "{}", self.msg)
    }
}

impl Error for MyError {}

// Or use thiserror:
use thiserror::Error;

#[derive(Error, Debug)]
enum AppError {
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),
}
```

### Marker Traits
```rust
// Send: can transfer between threads
// Sync: can share (&T) between threads

// Most types auto-implement Send + Sync
struct SafeType { data: i32 }  // Send + Sync

// Copy for simple types
#[derive(Copy, Clone)]
struct Point { x: i32, y: i32 }

// PhantomData for type markers
use std::marker::PhantomData;
struct Container<T> {
    id: u32,
    _phantom: PhantomData<T>,
}
```

## Quick Reference

### std::mem Functions
| Function | Description |
|----------|-------------|
| `size_of::<T>()` | Size of type in bytes |
| `align_of::<T>()` | Alignment of type |
| `swap(&mut a, &mut b)` | Swap two values |
| `replace(&mut dest, src)` | Replace, return old |
| `take(&mut dest)` | Take, leave Default |
| `drop(val)` | Run destructor now |
| `forget(val)` | Skip destructor |

### Format Specifiers
| Spec | Trait | Example |
|------|-------|---------|
| `{}` | Display | User output |
| `{:?}` | Debug | Developer output |
| `{:#?}` | Debug | Pretty print |
| `{:x}` | LowerHex | `ff` |
| `{:b}` | Binary | `1010` |

### Marker Traits
| Trait | Meaning |
|-------|---------|
| `Send` | Can move between threads |
| `Sync` | Can share &T between threads |
| `Copy` | Implicit bit copy |
| `Sized` | Known size at compile time |
| `Unpin` | Can be moved after pinning |

## Thread Safety Quick Check

| Type | Send | Sync |
|------|------|------|
| Primitives | Yes | Yes |
| `Rc<T>` | No | No |
| `Arc<T>` | Yes* | Yes* |
| `Mutex<T>` | Yes* | Yes* |
| `Cell<T>` | Yes* | No |
| `RefCell<T>` | Yes* | No |

*If T satisfies requirements

## When to Use What

- **mem::swap**: Exchange values without temp variable
- **mem::take**: Move out of mutable reference
- **mem::replace**: Swap in new value, get old
- **Display**: User-facing output
- **Debug**: Developer debugging
- **thiserror**: Library error types
- **anyhow**: Application error handling
- **PhantomData**: Unused type parameters

## When Writing Code

1. Derive `Debug` for all public types
2. Implement `Display` for user-visible types
3. Use `thiserror` for custom errors in libraries
4. Use `anyhow` for error handling in applications
5. Check Send/Sync requirements for concurrent code
6. Use `mem::take` to move out of mutable references
