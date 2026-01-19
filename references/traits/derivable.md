# Derivable Traits Reference

## Standard Derivable Traits

| Trait | Purpose | Requirements |
|-------|---------|--------------|
| `Clone` | Explicit copy | All fields Clone |
| `Copy` | Implicit copy | All fields Copy, no Drop |
| `Debug` | Debug formatting | All fields Debug |
| `Default` | Default value | All fields Default |
| `PartialEq` | `==` comparison | All fields PartialEq |
| `Eq` | Total equality | PartialEq + no NaN-like values |
| `PartialOrd` | `<`, `>` comparison | PartialEq + all fields PartialOrd |
| `Ord` | Total ordering | PartialOrd + Eq + all fields Ord |
| `Hash` | Hashing | All fields Hash |

## Common Combinations

### Simple Value Type
```rust
#[derive(Debug, Clone, Copy, PartialEq, Eq, Hash, Default)]
struct Point {
    x: i32,
    y: i32,
}
```

### Struct with Strings
```rust
#[derive(Debug, Clone, PartialEq, Eq, Hash, Default)]
struct Person {
    name: String,  // String is not Copy
    age: u32,
}
```

### Ordered Type
```rust
#[derive(Debug, Clone, PartialEq, Eq, PartialOrd, Ord, Hash)]
struct Version {
    major: u32,
    minor: u32,
    patch: u32,
}
```

### Enum
```rust
#[derive(Debug, Clone, Copy, PartialEq, Eq, Hash)]
enum Status {
    Active,
    Inactive,
    Pending,
}
```

## Order Matters

For `Ord` and `PartialOrd`, fields are compared in declaration order:

```rust
#[derive(PartialOrd, Ord, PartialEq, Eq)]
struct Version {
    major: u32,  // Compared first
    minor: u32,  // Then this
    patch: u32,  // Then this
}
```

## Cannot Derive When

| Trait | Cannot derive when |
|-------|-------------------|
| `Clone` | Field doesn't implement Clone |
| `Copy` | Field is not Copy, or type has Drop |
| `Default` | Field doesn't implement Default |
| `PartialEq`/`Eq` | Field doesn't implement trait |
| `Hash` | Field doesn't implement Hash |
| `Ord` | Field contains floats (f32/f64) |

## Manual Implementation

When derive doesn't work or you need custom behavior:

```rust
impl Clone for MyType {
    fn clone(&self) -> Self {
        // Custom clone logic
    }
}

impl Default for MyType {
    fn default() -> Self {
        MyType {
            field: custom_default(),
        }
    }
}
```
