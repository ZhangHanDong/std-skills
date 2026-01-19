# Conversion Traits Reference

## From / Into

Infallible conversion between types.

```rust
// Implement From, get Into for free
impl From<i32> for MyType {
    fn from(val: i32) -> Self {
        MyType { value: val }
    }
}

// Usage
let my = MyType::from(42);
let my: MyType = 42.into();
```

### Standard From Implementations

| From | To | Notes |
|------|-----|-------|
| `&str` | `String` | Allocates |
| `String` | `Vec<u8>` | `into_bytes()` |
| `Vec<u8>` | `String` | Use `TryFrom` (can fail) |
| `&[T]` | `Vec<T>` | Clones elements |
| `T` | `Option<T>` | `Some(t)` |
| `T` | `Result<T, E>` | `Ok(t)` |
| `i32` | `i64` | Widening |

## TryFrom / TryInto

Fallible conversion.

```rust
impl TryFrom<i64> for MySmallType {
    type Error = &'static str;

    fn try_from(value: i64) -> Result<Self, Self::Error> {
        if value > 100 {
            Err("value too large")
        } else {
            Ok(MySmallType(value as i32))
        }
    }
}

// Usage
let small: MySmallType = 50i64.try_into()?;
let result: Result<MySmallType, _> = 200i64.try_into();
```

## AsRef / AsMut

Cheap reference conversion.

```rust
// Accept anything that can be viewed as &[u8]
fn process(data: impl AsRef<[u8]>) {
    let bytes = data.as_ref();
}

// Works with:
process(b"bytes");
process(vec![1, 2, 3]);
process(&[1, 2, 3]);
```

### Common AsRef Implementations

| Type | AsRef Target |
|------|--------------|
| `String` | `str`, `[u8]`, `Path` |
| `Vec<T>` | `[T]` |
| `PathBuf` | `Path` |
| `Box<T>` | `T` |

## Borrow

Similar to AsRef but with additional guarantees for Hash/Eq.

```rust
use std::borrow::Borrow;

fn lookup<Q>(map: &HashMap<String, i32>, key: &Q) -> Option<&i32>
where
    String: Borrow<Q>,
    Q: Hash + Eq + ?Sized,
{
    map.get(key)
}

// Can lookup with &str even though key is String
let val = lookup(&map, "key");
```

## Deref / DerefMut

Smart pointer dereference.

```rust
use std::ops::Deref;

struct MyBox<T>(T);

impl<T> Deref for MyBox<T> {
    type Target = T;

    fn deref(&self) -> &Self::Target {
        &self.0
    }
}

// Enables:
// - *my_box
// - Deref coercion (MyBox<String> -> &str)
```

## Comparison

| Trait | Purpose | Cost | Use Case |
|-------|---------|------|----------|
| `From`/`Into` | Conversion | May allocate | Type transformation |
| `AsRef` | View as reference | Cheap | Function parameters |
| `Borrow` | Borrowed form | Cheap | HashMap lookup |
| `Deref` | Smart pointer | Cheap | Custom pointer types |
