---
name: rust-std-collections
description: |
  CRITICAL: Use for Rust std collections. Triggers on:
  Vec, HashMap, HashSet, BTreeMap, BTreeSet, VecDeque, LinkedList,
  BinaryHeap, collections, vec!, hashmap!, push, pop, insert, get,
  集合, 向量, 哈希表, 数组, 容器
---

# Rust Standard Collections

> **Version:** 1.92.0 | **Last Updated:** 2026-01-19
>
> Source: https://doc.rust-lang.org/std/collections/

Help users with Rust standard library collections.

## Documentation

Refer to the local files for detailed documentation:
- `../../references/collections/vec.md` - Vec usage patterns
- `../../references/collections/hashmap.md` - HashMap and HashSet
- `../../references/_shared/rust-defaults.md` - Rust code generation defaults

## Collection Types

| Type | Use Case | Ordered | Duplicates |
|------|----------|---------|------------|
| `Vec<T>` | General purpose array | Index-based | Yes |
| `VecDeque<T>` | Double-ended queue | Index-based | Yes |
| `LinkedList<T>` | Fast insert/remove | Order preserved | Yes |
| `HashMap<K, V>` | Key-value lookup | No | No (keys) |
| `BTreeMap<K, V>` | Sorted key-value | By key | No (keys) |
| `HashSet<T>` | Unique values | No | No |
| `BTreeSet<T>` | Sorted unique values | By value | No |
| `BinaryHeap<T>` | Priority queue | Max-first | Yes |

## Key Patterns

### Vec (Dynamic Array)
```rust
// Creation
let mut v: Vec<i32> = Vec::new();
let v = vec![1, 2, 3];
let v = Vec::with_capacity(100);

// Operations
v.push(4);
v.pop();  // Returns Option<T>
v.insert(0, 0);
v.remove(0);

// Access
let first = v[0];           // Panics if out of bounds
let first = v.get(0);       // Returns Option<&T>
let slice = &v[1..3];

// Iteration
for item in &v { }          // Borrow
for item in &mut v { }      // Mutable borrow
for item in v { }           // Consume
```

### HashMap (Key-Value Store)
```rust
use std::collections::HashMap;

let mut map = HashMap::new();
map.insert("key", "value");

// Access
let val = map.get("key");           // Option<&V>
let val = map["key"];               // Panics if missing

// Entry API (insert if missing)
map.entry("key").or_insert("default");
map.entry("key").or_insert_with(|| expensive_fn());

// Iteration
for (k, v) in &map { }
```

### VecDeque (Double-Ended Queue)
```rust
use std::collections::VecDeque;

let mut deque = VecDeque::new();
deque.push_back(1);
deque.push_front(0);
let front = deque.pop_front();
let back = deque.pop_back();
```

### BTreeMap (Sorted Map)
```rust
use std::collections::BTreeMap;

let mut map = BTreeMap::new();
map.insert(3, "c");
map.insert(1, "a");

// Iterate in sorted key order
for (k, v) in &map {
    println!("{}: {}", k, v);  // 1: a, 3: c
}

// Range queries
for (k, v) in map.range(1..3) { }
```

## API Reference Table

| Method | Vec | HashMap | VecDeque |
|--------|-----|---------|----------|
| Create empty | `new()` | `new()` | `new()` |
| With capacity | `with_capacity(n)` | `with_capacity(n)` | `with_capacity(n)` |
| Add element | `push(v)` | `insert(k, v)` | `push_back(v)` |
| Remove element | `pop()` | `remove(&k)` | `pop_back()` |
| Get element | `get(i)` | `get(&k)` | `get(i)` |
| Length | `len()` | `len()` | `len()` |
| Check empty | `is_empty()` | `is_empty()` | `is_empty()` |
| Clear | `clear()` | `clear()` | `clear()` |

## When to Use Which

- **Vec**: Default choice for sequences
- **VecDeque**: Need push/pop from both ends
- **HashMap**: Fast key lookup, don't need order
- **BTreeMap**: Need sorted keys or range queries
- **HashSet**: Need unique values, fast lookup
- **BTreeSet**: Need sorted unique values
- **BinaryHeap**: Need to always access max element

## When Writing Code

1. Use `Vec::with_capacity()` when size is known
2. Use Entry API for conditional HashMap inserts
3. Prefer `get()` over indexing to avoid panics
4. Use `iter()`, `iter_mut()`, `into_iter()` appropriately
5. Consider `drain()` for efficient removal while iterating
