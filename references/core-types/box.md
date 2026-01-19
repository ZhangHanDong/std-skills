# Box Reference

## Definition

`Box<T>` - A pointer to heap-allocated data with unique ownership.

## Creation

```rust
let boxed = Box::new(5);
let boxed: Box<i32> = Box::new(5);
```

## When to Use

1. **Recursive types** (required):
```rust
enum List<T> {
    Cons(T, Box<List<T>>),
    Nil,
}
```

2. **Large data on heap**:
```rust
let large = Box::new([0u8; 1_000_000]);
```

3. **Trait objects**:
```rust
let obj: Box<dyn Trait> = Box::new(concrete);
```

4. **Transfer ownership without copying**:
```rust
fn takes_box(b: Box<LargeStruct>) { }
```

## Dereferencing

```rust
let boxed = Box::new(5);
let val: i32 = *boxed;  // Move out of box
```

## Common Methods

| Method | Description |
|--------|-------------|
| `Box::new(val)` | Create new box |
| `Box::into_raw(b)` | Convert to raw pointer |
| `Box::from_raw(ptr)` | Create from raw pointer (unsafe) |
| `Box::leak(b)` | Leak into `&'static mut T` |
| `Box::into_inner(b)` | Unwrap the value |

## Raw Pointer Interop

```rust
let b = Box::new(5);
let ptr = Box::into_raw(b);

// Later...
let b = unsafe { Box::from_raw(ptr) };
```

## Traits Implemented

- `Deref<Target = T>` - Use like `&T`
- `DerefMut` - Use like `&mut T`
- `Drop` - Automatic cleanup
- `Clone` (if T: Clone)
- `Send`, `Sync` (if T is)

## C Interop

`Box<T>` for `T: Sized` is ABI-compatible with C pointers:

```rust
#[repr(C)]
pub struct Foo { /* ... */ }

#[no_mangle]
pub extern "C" fn foo_new() -> Box<Foo> {
    Box::new(Foo { /* ... */ })
}

#[no_mangle]
pub extern "C" fn foo_delete(_: Option<Box<Foo>>) {}
```

## Box vs Other Smart Pointers

| Type | Ownership | Thread-safe | Ref count |
|------|-----------|-------------|-----------|
| `Box<T>` | Unique | Yes* | No |
| `Rc<T>` | Shared | No | Yes |
| `Arc<T>` | Shared | Yes | Yes |

*If T is Send/Sync
