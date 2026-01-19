# Marker Traits (std::marker) Reference

## Send and Sync

### Send
Type can be transferred to another thread.

```rust
// Automatically derived for most types
struct MyStruct {
    data: Vec<i32>,  // Vec is Send
}

// NOT Send
use std::rc::Rc;
let rc = Rc::new(5);  // Rc is not Send
// thread::spawn(move || { rc; });  // Won't compile
```

### Sync
Type can be shared via `&T` between threads.

```rust
// T is Sync if &T is Send
// i.e., safe to access from multiple threads

// NOT Sync
use std::cell::Cell;
let cell = Cell::new(5);  // Cell is not Sync
```

### Common Type Thread Safety

| Type | Send | Sync |
|------|------|------|
| `i32`, `bool`, primitives | Yes | Yes |
| `String`, `Vec<T>` | Yes* | Yes* |
| `Rc<T>` | No | No |
| `Arc<T>` | Yes* | Yes* |
| `Mutex<T>` | Yes* | Yes* |
| `Cell<T>` | Yes* | No |
| `RefCell<T>` | Yes* | No |
| Raw pointers | No | No |

*If inner type T is Send/Sync

## Copy

Type can be duplicated by copying bits.

```rust
#[derive(Copy, Clone)]
struct Point {
    x: i32,
    y: i32,
}

let p1 = Point { x: 1, y: 2 };
let p2 = p1;  // Copies, p1 still valid
println!("{}, {}", p1.x, p2.x);

// Cannot derive Copy if any field is not Copy
// struct NoCopy {
//     data: String,  // String is not Copy
// }
```

### Copy Requirements
- All fields must be Copy
- Cannot have Drop implementation
- Typically for small, simple values

## Sized

Type has known size at compile time.

```rust
// Most types are Sized
fn foo<T>(t: T) { }  // T: Sized is implicit

// Unsized types (DSTs)
fn bar<T: ?Sized>(t: &T) { }  // Accept unsized too
// str, [T], dyn Trait are unsized
```

## Unpin

Type doesn't require pinning guarantees.

```rust
// Most types are Unpin by default
struct Normal { x: i32 }  // Unpin

// Self-referential types need !Unpin
use std::marker::PhantomPinned;
struct SelfRef {
    data: String,
    ptr: *const String,  // Points to data
    _pin: PhantomPinned,
}
```

## PhantomData

Zero-sized marker for type relationships.

```rust
use std::marker::PhantomData;

// Indicate ownership without storing
struct Iter<'a, T> {
    ptr: *const T,
    end: *const T,
    _marker: PhantomData<&'a T>,  // Lifetime marker
}

// Unused type parameter
struct MyStruct<T> {
    id: u32,
    _phantom: PhantomData<T>,
}
```

### PhantomData Use Cases

```rust
// 1. Lifetime marker
struct Ref<'a, T> {
    data: *const T,
    _marker: PhantomData<&'a T>,
}

// 2. Ownership marker
struct Unique<T> {
    ptr: *const T,
    _marker: PhantomData<T>,  // "Owns" T
}

// 3. Type tag (zero-cost)
struct Meters;
struct Feet;
struct Distance<U> {
    value: f64,
    _unit: PhantomData<U>,
}
```

## Implementing Marker Traits

```rust
// Usually automatic, but can be manual

// Unsafe: assert thread safety
unsafe impl Send for MyType {}
unsafe impl Sync for MyType {}

// Opt out of auto-impl
impl !Send for NotSend {}  // Unstable
impl !Sync for NotSync {}  // Unstable

// Use PhantomData to prevent auto-impl
use std::cell::UnsafeCell;
struct NotSync {
    _marker: PhantomData<UnsafeCell<()>>,
}
```

## Summary

| Trait | Meaning | Derive |
|-------|---------|--------|
| `Send` | Can move between threads | Auto |
| `Sync` | Can share between threads | Auto |
| `Copy` | Bitwise copyable | `#[derive(Copy, Clone)]` |
| `Sized` | Known compile-time size | Auto |
| `Unpin` | No pinning needed | Auto |
