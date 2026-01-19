# Cell Types Reference

## Overview

Interior mutability: Mutate through `&T` instead of requiring `&mut T`.

| Type | Copy Req | Runtime Check | Use Case |
|------|----------|---------------|----------|
| `Cell<T>` | Yes | No | Simple Copy types |
| `RefCell<T>` | No | Yes | Complex types |
| `OnceCell<T>` | No | No | Write-once values |
| `LazyCell<T>` | No | No | Lazy initialization |

## Cell<T>

For `Copy` types. Never gives references to inner value.

```rust
use std::cell::Cell;

let cell = Cell::new(5);
cell.set(10);
let val = cell.get();  // Copies out
```

### Methods

| Method | Description |
|--------|-------------|
| `get()` | Copy out value |
| `set(val)` | Replace value |
| `replace(val)` | Replace, return old |
| `take()` | Replace with Default |
| `into_inner()` | Consume cell |

## RefCell<T>

Runtime borrow checking. Panics on violation.

```rust
use std::cell::RefCell;

let cell = RefCell::new(vec![1, 2, 3]);

// Immutable borrow
let borrowed = cell.borrow();
println!("{:?}", *borrowed);
drop(borrowed);

// Mutable borrow
cell.borrow_mut().push(4);
```

### Methods

| Method | Description |
|--------|-------------|
| `borrow()` | Immutable borrow (`Ref<T>`) |
| `borrow_mut()` | Mutable borrow (`RefMut<T>`) |
| `try_borrow()` | Returns `Result` |
| `try_borrow_mut()` | Returns `Result` |
| `into_inner()` | Consume cell |

### Borrow Rules (Runtime)

- Multiple `borrow()` allowed
- One `borrow_mut()` allowed
- Never both simultaneously
- **Panics if violated!**

## OnceCell<T>

Write-once, then read without checks.

```rust
use std::cell::OnceCell;

let cell: OnceCell<String> = OnceCell::new();
assert!(cell.get().is_none());

cell.set("hello".to_string()).unwrap();
assert_eq!(cell.get(), Some(&"hello".to_string()));
```

### Methods

| Method | Description |
|--------|-------------|
| `get()` | Get reference if set |
| `set(val)` | Set if empty |
| `get_or_init(f)` | Get or initialize |
| `get_mut()` | Mutable reference |
| `into_inner()` | Consume cell |

### Use Case: Lazy Caching

```rust
struct Graph {
    edges: Vec<(i32, i32)>,
    cache: OnceCell<Vec<(i32, i32)>>,
}

impl Graph {
    fn spanning_tree(&self) -> &Vec<(i32, i32)> {
        self.cache.get_or_init(|| self.compute_tree())
    }
}
```

## LazyCell<T, F>

Combines `OnceCell` with initialization function.

```rust
use std::cell::LazyCell;

let lazy = LazyCell::new(|| expensive_computation());
// Not computed yet

let value = *lazy;  // Computed on first access
```

## Common Patterns

### Rc + RefCell (Shared Mutable State)

```rust
use std::rc::Rc;
use std::cell::RefCell;

let shared = Rc::new(RefCell::new(vec![]));
let clone = Rc::clone(&shared);

shared.borrow_mut().push(1);
clone.borrow_mut().push(2);
```

### Caching in Immutable Struct

```rust
struct Cached {
    value: i32,
    computed: OnceCell<i32>,
}

impl Cached {
    fn get_computed(&self) -> i32 {
        *self.computed.get_or_init(|| self.value * 2)
    }
}
```

## Thread-Safe Equivalents

| Single-threaded | Multi-threaded |
|-----------------|----------------|
| `Cell<T>` | `Atomic*` |
| `RefCell<T>` | `RwLock<T>` |
| `OnceCell<T>` | `OnceLock<T>` |
| `LazyCell<T>` | `LazyLock<T>` |
