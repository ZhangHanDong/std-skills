# Vec Reference

## Creation

```rust
// Empty
let v: Vec<i32> = Vec::new();
let v: Vec<i32> = vec![];

// With values
let v = vec![1, 2, 3];
let v = vec![0; 10];  // 10 zeros

// With capacity
let v: Vec<i32> = Vec::with_capacity(100);
```

## Common Methods

| Method | Description | Returns |
|--------|-------------|---------|
| `push(val)` | Add to end | `()` |
| `pop()` | Remove from end | `Option<T>` |
| `insert(idx, val)` | Insert at index | `()` |
| `remove(idx)` | Remove at index | `T` |
| `get(idx)` | Safe access | `Option<&T>` |
| `first()` / `last()` | First/last element | `Option<&T>` |
| `len()` | Number of elements | `usize` |
| `is_empty()` | Check if empty | `bool` |
| `clear()` | Remove all elements | `()` |
| `contains(&val)` | Check membership | `bool` |
| `sort()` | Sort in place | `()` |
| `dedup()` | Remove consecutive duplicates | `()` |
| `retain(f)` | Keep elements matching predicate | `()` |
| `drain(range)` | Remove and iterate range | `Drain<T>` |

## Iteration

```rust
// Immutable reference
for item in &vec { }
for item in vec.iter() { }

// Mutable reference
for item in &mut vec { }
for item in vec.iter_mut() { }

// Consuming
for item in vec { }
for item in vec.into_iter() { }
```

## Conversion

```rust
// Vec <-> Slice
let slice: &[i32] = &vec;
let vec: Vec<i32> = slice.to_vec();

// Vec <-> Array
let arr: [i32; 3] = vec.try_into().unwrap();
let vec: Vec<i32> = arr.into();

// Vec<u8> <-> String
let vec: Vec<u8> = string.into_bytes();
let string = String::from_utf8(vec)?;
```

## Performance Tips

- Use `with_capacity()` when size is known
- Use `extend()` instead of multiple `push()`
- Use `drain()` for efficient removal while iterating
- `pop()` is O(1), `remove(0)` is O(n)
