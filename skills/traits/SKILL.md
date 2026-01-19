---
name: rust-std-traits
description: |
  CRITICAL: Use for Rust std traits. Triggers on:
  Iterator, From, Into, Clone, Copy, Default, Debug, Display,
  PartialEq, Eq, PartialOrd, Ord, Hash, Deref, Drop, AsRef, Borrow,
  trait, derive, impl, 特征, 派生, 实现
---

# Rust Standard Traits

> **Version:** 1.92.0 | **Last Updated:** 2026-01-19
>
> Source: https://doc.rust-lang.org/std/

Help users with Rust standard library traits.

## Documentation

Refer to the local files for detailed documentation:
- `../../references/traits/derivable.md` - Derivable traits guide
- `../../references/traits/conversion.md` - From/Into patterns
- `../../references/_shared/rust-defaults.md` - Rust code generation defaults

## Trait Categories

### Derivable Traits

| Trait | Purpose | Derive |
|-------|---------|--------|
| `Clone` | Explicit copy | `#[derive(Clone)]` |
| `Copy` | Implicit copy (stack only) | `#[derive(Copy, Clone)]` |
| `Debug` | Debug formatting | `#[derive(Debug)]` |
| `Default` | Default value | `#[derive(Default)]` |
| `PartialEq` | Equality comparison | `#[derive(PartialEq)]` |
| `Eq` | Total equality | `#[derive(Eq, PartialEq)]` |
| `PartialOrd` | Partial ordering | `#[derive(PartialOrd)]` |
| `Ord` | Total ordering | `#[derive(Ord, PartialOrd, Eq, PartialEq)]` |
| `Hash` | Hashable | `#[derive(Hash)]` |

### Marker Traits

| Trait | Module | Meaning |
|-------|--------|---------|
| `Send` | `marker` | Safe to send across threads |
| `Sync` | `marker` | Safe to share references across threads |
| `Copy` | `marker` | Bitwise copyable |
| `Sized` | `marker` | Known size at compile time |
| `Unpin` | `marker` | Safe to move after pinning |

### Conversion Traits

| Trait | Method | Usage |
|-------|--------|-------|
| `From<T>` | `from(T) -> Self` | Infallible conversion |
| `Into<T>` | `into(self) -> T` | Infallible conversion |
| `TryFrom<T>` | `try_from(T) -> Result` | Fallible conversion |
| `TryInto<T>` | `try_into(self) -> Result` | Fallible conversion |
| `AsRef<T>` | `as_ref(&self) -> &T` | Cheap reference conversion |
| `AsMut<T>` | `as_mut(&mut self) -> &mut T` | Cheap mutable reference |
| `Borrow<T>` | `borrow(&self) -> &T` | Borrowed form |

### Operator Traits

| Trait | Operator | Module |
|-------|----------|--------|
| `Deref` | `*` (deref) | `ops` |
| `DerefMut` | `*` (mut deref) | `ops` |
| `Drop` | destructor | `ops` |
| `Index` | `[]` | `ops` |
| `Add`, `Sub`, etc. | `+`, `-`, etc. | `ops` |

## Key Patterns

### From/Into Conversion
```rust
// Implement From, get Into for free
impl From<String> for MyType {
    fn from(s: String) -> Self {
        MyType { value: s }
    }
}

// Usage
let my = MyType::from("hello".to_string());
let my: MyType = "hello".to_string().into();

// In function parameters - accept anything that converts
fn process(input: impl Into<MyType>) {
    let my = input.into();
}
```

### AsRef for Flexible Parameters
```rust
// Accept &str, &String, &Path, etc.
fn read_file(path: impl AsRef<std::path::Path>) {
    let path = path.as_ref();
    // ...
}

// Usage
read_file("file.txt");
read_file(String::from("file.txt"));
read_file(std::path::Path::new("file.txt"));
```

### Iterator Trait
```rust
struct Counter {
    count: u32,
    max: u32,
}

impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        if self.count < self.max {
            self.count += 1;
            Some(self.count)
        } else {
            None
        }
    }
}
```

### Deref for Smart Pointers
```rust
use std::ops::Deref;

struct MyBox<T>(T);

impl<T> Deref for MyBox<T> {
    type Target = T;

    fn deref(&self) -> &Self::Target {
        &self.0
    }
}

// Enables *my_box and deref coercion
```

### Drop for Cleanup
```rust
struct Resource {
    name: String,
}

impl Drop for Resource {
    fn drop(&mut self) {
        println!("Dropping {}", self.name);
    }
}
```

## Common Derive Combinations

```rust
// Value type (copyable)
#[derive(Debug, Clone, Copy, PartialEq, Eq, Hash, Default)]
struct Point { x: i32, y: i32 }

// String-containing type (not copyable)
#[derive(Debug, Clone, PartialEq, Eq, Hash, Default)]
struct Person { name: String }

// Ordered type
#[derive(Debug, Clone, PartialEq, Eq, PartialOrd, Ord)]
struct Version { major: u32, minor: u32 }
```

## Trait Bounds

```rust
// Single bound
fn print_debug<T: Debug>(item: T) { }

// Multiple bounds
fn compare<T: PartialEq + Debug>(a: T, b: T) { }

// Where clause
fn complex<T, U>(t: T, u: U)
where
    T: Clone + Debug,
    U: From<T>,
{ }
```

## When Writing Code

1. Implement `From` instead of `Into` (Into is derived)
2. Derive traits when possible, manual impl when needed
3. Use `AsRef`/`Borrow` for flexible function parameters
4. Implement `Deref` for smart pointer types
5. Always implement `Debug` for public types
6. `Copy` requires `Clone` - derive both together

## When Answering Questions

1. Explain trait bounds and their implications
2. Mention orphan rules for trait implementations
3. Suggest appropriate derive macros
4. Explain difference between `AsRef` vs `Borrow` vs `Deref`
