# Option Reference

## Definition

```rust
pub enum Option<T> {
    Some(T),  // Contains a value
    None,     // No value
}
```

## Common Uses

- Initial values and optional fields
- Return values for partial functions
- Simple error reporting (None on error)
- Nullable pointers pattern

## Querying

| Method | Description |
|--------|-------------|
| `is_some()` | Returns `true` if `Some` |
| `is_none()` | Returns `true` if `None` |
| `is_some_and(f)` | `Some` and predicate is true |

## Extracting Values

```rust
// Panics on None
.expect("message")
.unwrap()

// Returns default
.unwrap_or(default)
.unwrap_or_default()
.unwrap_or_else(|| compute())
```

## Transforming

```rust
// Map and filter
.map(|x| x + 1)
.filter(|x| x > &5)
.flatten()

// To Result
.ok_or(err)
.ok_or_else(|| compute_err())
.transpose()  // Option<Result> -> Result<Option>

// Combining
.zip(other)
.zip_with(other, |a, b| a + b)
```

## Boolean Operators

```rust
// Eager (takes Option)
.and(other)   // Returns other if Some
.or(other)    // Returns self if Some, else other
.xor(other)   // Returns Some if exactly one is Some

// Lazy (takes closure)
.and_then(|x| some_fn(x))
.or_else(|| Some(default))
```

## References

```rust
.as_ref()       // &Option<T> -> Option<&T>
.as_mut()       // &mut Option<T> -> Option<&mut T>
.as_deref()     // Option<T> -> Option<&T::Target>
.as_slice()     // &Option<T> -> &[T] (0 or 1 element)
```

## In-Place Modification

```rust
.insert(val)              // Insert and return &mut
.get_or_insert(default)   // Get or insert default
.get_or_insert_with(f)    // Get or insert computed
.take()                   // Take value, leave None
.replace(val)             // Replace and return old
```

## The ? Operator

```rust
fn add_last(stack: &mut Vec<i32>) -> Option<i32> {
    Some(stack.pop()? + stack.pop()?)
}
```

Returns `None` early if `None`, otherwise unwraps.

## Iteration

```rust
for item in opt { }           // Consumes, 0-1 values
for item in opt.iter() { }    // &T iterator
```

## Comparison

- `None < Some(anything)`
- `Some` values compare by inner value

## Null Pointer Optimization

`Option<T>` has same size as `T` for:
- `Box<T>`, `&T`, `&mut T`
- Function pointers
- `NonZero*` types
- `NonNull<T>`
