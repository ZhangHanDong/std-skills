# HashMap and HashSet Reference

## HashMap

### Creation

```rust
use std::collections::HashMap;

let mut map: HashMap<&str, i32> = HashMap::new();
let map = HashMap::from([("a", 1), ("b", 2)]);
let map: HashMap<_, _> = vec![("a", 1), ("b", 2)].into_iter().collect();
```

### Common Methods

| Method | Description | Returns |
|--------|-------------|---------|
| `insert(k, v)` | Insert or replace | `Option<V>` (old value) |
| `get(&k)` | Get reference | `Option<&V>` |
| `get_mut(&k)` | Get mutable reference | `Option<&mut V>` |
| `remove(&k)` | Remove entry | `Option<V>` |
| `contains_key(&k)` | Check key exists | `bool` |
| `len()` | Number of entries | `usize` |
| `is_empty()` | Check if empty | `bool` |
| `clear()` | Remove all | `()` |
| `keys()` | Iterate keys | `Keys<K, V>` |
| `values()` | Iterate values | `Values<K, V>` |

### Entry API

```rust
// Insert if missing
map.entry("key").or_insert(0);

// Insert with computation
map.entry("key").or_insert_with(|| expensive_fn());

// Modify existing
map.entry("key").and_modify(|v| *v += 1).or_insert(1);

// Get or insert default
let count = map.entry("key").or_default();
*count += 1;
```

### Iteration

```rust
// Key-value pairs
for (k, v) in &map { }

// Only keys
for k in map.keys() { }

// Only values
for v in map.values() { }

// Mutable values
for v in map.values_mut() { }
```

## HashSet

### Creation

```rust
use std::collections::HashSet;

let mut set: HashSet<i32> = HashSet::new();
let set = HashSet::from([1, 2, 3]);
let set: HashSet<_> = vec![1, 2, 3].into_iter().collect();
```

### Common Methods

| Method | Description | Returns |
|--------|-------------|---------|
| `insert(v)` | Add element | `bool` (true if new) |
| `remove(&v)` | Remove element | `bool` |
| `contains(&v)` | Check membership | `bool` |
| `len()` | Number of elements | `usize` |

### Set Operations

```rust
let a: HashSet<_> = [1, 2, 3].into();
let b: HashSet<_> = [2, 3, 4].into();

// Union: {1, 2, 3, 4}
let union: HashSet<_> = a.union(&b).copied().collect();

// Intersection: {2, 3}
let inter: HashSet<_> = a.intersection(&b).copied().collect();

// Difference: {1}
let diff: HashSet<_> = a.difference(&b).copied().collect();

// Symmetric difference: {1, 4}
let sym: HashSet<_> = a.symmetric_difference(&b).copied().collect();

// Subset check
a.is_subset(&b);
a.is_superset(&b);
a.is_disjoint(&b);
```

## Key Requirements

Keys must implement `Eq + Hash`:

```rust
#[derive(Hash, Eq, PartialEq)]
struct MyKey {
    id: u64,
}
```
