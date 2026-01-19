# Result Reference

## Definition

```rust
pub enum Result<T, E> {
    Ok(T),   // Success with value
    Err(E),  // Error with value
}
```

## Querying

| Method | Description |
|--------|-------------|
| `is_ok()` | Returns `true` if `Ok` |
| `is_err()` | Returns `true` if `Err` |
| `is_ok_and(f)` | `Ok` and predicate is true |
| `is_err_and(f)` | `Err` and predicate is true |

## Extracting Values

```rust
// For Ok (panics if Err)
.unwrap()
.expect("message")
.unwrap_or(default)
.unwrap_or_default()
.unwrap_or_else(|e| handle(e))

// For Err (panics if Ok)
.unwrap_err()
.expect_err("message")
```

## Transforming

```rust
// To Option
.ok()    // Result<T, E> -> Option<T>
.err()   // Result<T, E> -> Option<E>

// Mapping
.map(|v| v + 1)           // Transform Ok value
.map_err(|e| wrap(e))     // Transform Err value
.map_or(default, |v| v)   // Map Ok or return default
.map_or_else(|e| default, |v| v)

// Inspection
.inspect(|v| println!("{}", v))
.inspect_err(|e| eprintln!("{}", e))
```

## Reference Adapters

```rust
.as_ref()       // &Result<T, E> -> Result<&T, &E>
.as_mut()       // &mut Result<T, E> -> Result<&mut T, &mut E>
.as_deref()     // Result<T, E> -> Result<&T::Target, &E>
.transpose()    // Result<Option<T>, E> -> Option<Result<T, E>>
```

## Boolean Operators

```rust
// Taking Result
.and(other)   // Returns other if Ok
.or(other)    // Returns self if Ok, else other

// Taking closure (lazy)
.and_then(|v| compute(v))  // Chain operations
.or_else(|e| recover(e))   // Error recovery
```

## The ? Operator

```rust
fn read_file() -> io::Result<String> {
    let mut file = File::open("file.txt")?;
    let mut s = String::new();
    file.read_to_string(&mut s)?;
    Ok(s)
}
```

Returns `Err` early, otherwise unwraps `Ok`.

## Collecting Results

```rust
// Fails on first error
let v: Vec<i32> = [Ok(1), Ok(2), Err("e")];
let res: Result<Vec<_>, _> = v.into_iter().collect();
// res = Err("e")

// Sum/Product with Results
let v = [Ok(1), Ok(2), Ok(3)];
let sum: Result<i32, _> = v.into_iter().sum();
// sum = Ok(6)
```

## Must Use

`Result` has `#[must_use]` attribute:

```rust
// Warning: unused Result
file.write_all(b"data");

// Correct
file.write_all(b"data")?;
file.write_all(b"data").expect("write failed");
let _ = file.write_all(b"data");  // Explicit ignore
```

## Comparison

- `Ok` < any `Err`
- Same variants compare by inner value
