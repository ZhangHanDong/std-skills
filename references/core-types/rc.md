# Rc and Weak Reference

## Definition

- `Rc<T>` - Single-threaded reference-counted pointer
- `Weak<T>` - Non-owning reference (doesn't prevent deallocation)

## Creation

```rust
use std::rc::Rc;

let rc = Rc::new(5);
let rc2 = Rc::clone(&rc);  // Increment count
let rc3 = rc.clone();      // Same as above
```

## When to Use

- Shared ownership in single-threaded code
- Graph structures with multiple references
- Caching and memoization

## Common Methods

| Method | Description |
|--------|-------------|
| `Rc::new(val)` | Create new Rc |
| `Rc::clone(&rc)` | Increment ref count |
| `Rc::strong_count(&rc)` | Number of Rc pointers |
| `Rc::weak_count(&rc)` | Number of Weak pointers |
| `Rc::downgrade(&rc)` | Create Weak pointer |
| `Rc::get_mut(&mut rc)` | Get &mut if unique |
| `Rc::make_mut(&mut rc)` | Clone if not unique |

## Weak Pointers

```rust
use std::rc::{Rc, Weak};

let rc = Rc::new(5);
let weak: Weak<i32> = Rc::downgrade(&rc);

// Access via upgrade (may fail)
if let Some(rc) = weak.upgrade() {
    println!("{}", *rc);
}
```

## Breaking Cycles

```rust
use std::rc::{Rc, Weak};
use std::cell::RefCell;

struct Node {
    value: i32,
    parent: RefCell<Weak<Node>>,    // Weak to parent
    children: RefCell<Vec<Rc<Node>>>, // Rc to children
}
```

## Interior Mutability Pattern

`Rc` provides shared ownership but not mutability. Combine with `RefCell`:

```rust
use std::rc::Rc;
use std::cell::RefCell;

let shared = Rc::new(RefCell::new(vec![]));
shared.borrow_mut().push(1);

let clone = Rc::clone(&shared);
clone.borrow_mut().push(2);
```

## Rc vs Arc

| Feature | Rc | Arc |
|---------|-----|-----|
| Thread-safe | No | Yes |
| Overhead | Lower | Higher |
| Send/Sync | No | Yes |
| Use case | Single-threaded | Multi-threaded |

## Memory Leak Warning

Cycles of `Rc` cause memory leaks:

```rust
// This leaks!
struct Node {
    next: Option<Rc<Node>>,
}
let a = Rc::new(Node { next: None });
let b = Rc::new(Node { next: Some(Rc::clone(&a)) });
// If a.next = Some(Rc::clone(&b)), cycle created!
```

Solution: Use `Weak` for back-references.
