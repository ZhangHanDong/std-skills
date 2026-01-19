---
name: rust-std-core-types
description: |
  CRITICAL: Use for Rust core types. Triggers on:
  Option, Some, None, Result, Ok, Err, String, &str, Box, Rc, Weak,
  Cell, RefCell, OnceCell, LazyCell, unwrap, expect, map, and_then,
  interior mutability, reference counting, heap allocation,
  Option 用法, Result 用法, 字符串, 智能指针, 内部可变性
---

# Rust Core Types

> **Version:** 1.92.0 | **Last Updated:** 2026-01-19
>
> Source: https://doc.rust-lang.org/std/

Help users with Rust fundamental types: Option, Result, String, Box, Rc, and Cell types.

## Documentation

Refer to the local files for detailed documentation:
- `../../references/core-types/option.md` - Option<T> patterns
- `../../references/core-types/result.md` - Result<T, E> patterns
- `../../references/core-types/string.md` - String and &str
- `../../references/core-types/box.md` - Box<T> heap allocation
- `../../references/core-types/rc.md` - Rc<T> and Weak<T>
- `../../references/core-types/cell.md` - Cell, RefCell, OnceCell
- `../../references/_shared/rust-defaults.md` - Rust code generation defaults

## Type Overview

| Type | Module | Description |
|------|--------|-------------|
| `Option<T>` | `option` | Optional value (Some/None) |
| `Result<T, E>` | `result` | Success/error result (Ok/Err) |
| `String` | `string` | Owned UTF-8 string |
| `&str` | primitive | Borrowed string slice |
| `Box<T>` | `boxed` | Heap-allocated value |
| `Rc<T>` | `rc` | Reference-counted pointer |
| `Weak<T>` | `rc` | Non-owning Rc reference |
| `Cell<T>` | `cell` | Interior mutability (Copy) |
| `RefCell<T>` | `cell` | Interior mutability (runtime) |
| `OnceCell<T>` | `cell` | Write-once container |

## Key Patterns

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

// Common chains
opt.as_ref().map(|v| v.len())
opt.filter(|v| *v > 10).unwrap_or_default()
```

### Result Handling
```rust
// ? operator (propagate errors)
let value = result?;

// Combinators
let mapped = result
    .map(|v| v * 2)
    .map_err(|e| format!("{}", e));

// Convert to Option
let opt = result.ok();
```

### String Operations
```rust
// Creation
let s = String::from("hello");
let s = "hello".to_string();

// Concatenation
let s = format!("{} {}", s1, s2);
s.push_str(" world");

// Slicing (careful: byte indices!)
let slice = &s[0..5];

// Conversion
let bytes: Vec<u8> = s.into_bytes();
let num: i32 = "42".parse()?;
```

### Heap Allocation with Box
```rust
// Simple heap allocation
let boxed = Box::new(5);

// Recursive types (required)
enum List<T> {
    Cons(T, Box<List<T>>),
    Nil,
}

// Trait objects
let obj: Box<dyn Trait> = Box::new(concrete);
```

### Reference Counting
```rust
use std::rc::Rc;

let shared = Rc::new(5);
let clone = Rc::clone(&shared);

// With interior mutability
use std::cell::RefCell;
let shared = Rc::new(RefCell::new(vec![]));
shared.borrow_mut().push(1);
```

### Interior Mutability
```rust
use std::cell::{Cell, RefCell};

// Cell for Copy types
let cell = Cell::new(5);
cell.set(10);
let val = cell.get();

// RefCell for complex types
let cell = RefCell::new(vec![1, 2]);
cell.borrow_mut().push(3);
```

## Quick Reference

### Option Methods
| Method | Description |
|--------|-------------|
| `is_some()` / `is_none()` | Check variant |
| `unwrap()` / `expect(msg)` | Extract or panic |
| `unwrap_or(default)` | Extract or use default |
| `map(f)` | Transform Some value |
| `and_then(f)` | Chain Option-returning fn |
| `ok_or(err)` | Convert to Result |

### Result Methods
| Method | Description |
|--------|-------------|
| `is_ok()` / `is_err()` | Check variant |
| `unwrap()` / `expect(msg)` | Extract or panic |
| `map(f)` / `map_err(f)` | Transform Ok/Err |
| `and_then(f)` | Chain Result-returning fn |
| `ok()` / `err()` | Convert to Option |

### String Methods
| Method | Description |
|--------|-------------|
| `push(char)` / `push_str(&str)` | Append |
| `len()` / `is_empty()` | Length info |
| `trim()` | Remove whitespace |
| `split(pat)` | Split into iterator |
| `contains(pat)` | Check substring |

## When to Use Which

- **Option**: Missing values, optional fields
- **Result**: Operations that can fail
- **String**: Owned, mutable text
- **&str**: Borrowed, immutable text
- **Box**: Single-owner heap data, recursive types
- **Rc**: Shared ownership (single-threaded)
- **Cell**: Simple interior mutability (Copy types)
- **RefCell**: Complex interior mutability (runtime checks)

## When Writing Code

1. Prefer `?` over `.unwrap()` for error handling
2. Use `&str` in function parameters, `String` for owned data
3. Use `Rc<RefCell<T>>` for shared mutable state
4. Use `Box` for recursive types and trait objects
5. Use `OnceCell` for lazy initialization
6. Always handle the `None`/`Err` cases explicitly
