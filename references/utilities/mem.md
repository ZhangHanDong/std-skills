# Memory (std::mem) Reference

## Size and Alignment

```rust
use std::mem;

// Size in bytes
mem::size_of::<i32>()      // 4
mem::size_of::<String>()   // 24 (ptr + len + cap)

// Size of value
let v = vec![1, 2, 3];
mem::size_of_val(&v)       // 24

// Alignment
mem::align_of::<i32>()     // 4
mem::align_of::<u8>()      // 1
```

## Value Operations

### swap
```rust
let mut x = 5;
let mut y = 10;
mem::swap(&mut x, &mut y);
assert_eq!(x, 10);
assert_eq!(y, 5);
```

### replace
```rust
let mut v = "hello".to_string();
let old = mem::replace(&mut v, "world".to_string());
assert_eq!(old, "hello");
assert_eq!(v, "world");
```

### take
```rust
let mut v = Some(42);
let taken = mem::take(&mut v);
assert_eq!(taken, Some(42));
assert_eq!(v, None);  // Replaced with Default

// Useful for moving out of mutable reference
struct MyStruct {
    data: Vec<i32>,
}
impl MyStruct {
    fn consume_data(&mut self) -> Vec<i32> {
        mem::take(&mut self.data)
    }
}
```

### drop
```rust
let v = vec![1, 2, 3];
mem::drop(v);  // Runs destructor immediately
// v is no longer usable
```

### forget
```rust
let v = vec![1, 2, 3];
mem::forget(v);  // Does NOT run destructor!
// Memory leak! Use with caution
```

## Type Inspection

```rust
// Check if drop is meaningful
mem::needs_drop::<i32>()      // false
mem::needs_drop::<String>()   // true

// Enum discriminant
enum Foo { A, B, C }
let a = Foo::A;
let b = Foo::B;
assert!(mem::discriminant(&a) != mem::discriminant(&b));

// Number of variants
mem::variant_count::<Foo>()   // 3
```

## MaybeUninit

Safe wrapper for uninitialized memory:

```rust
use std::mem::MaybeUninit;

// Create uninitialized
let mut x: MaybeUninit<i32> = MaybeUninit::uninit();

// Initialize
x.write(42);

// Read (after initialization)
let val = unsafe { x.assume_init() };

// Array initialization pattern
let mut arr: [MaybeUninit<i32>; 10] = unsafe {
    MaybeUninit::uninit().assume_init()
};
for (i, elem) in arr.iter_mut().enumerate() {
    elem.write(i as i32);
}
let arr = unsafe {
    std::mem::transmute::<_, [i32; 10]>(arr)
};
```

## ManuallyDrop

Prevent automatic drop:

```rust
use std::mem::ManuallyDrop;

let mut x = ManuallyDrop::new(String::from("hello"));
// String won't be dropped automatically

// Manually drop when needed
unsafe { ManuallyDrop::drop(&mut x); }
```

## Common Patterns

### Move Out of Struct Field
```rust
struct Container {
    data: Option<String>,
}

impl Container {
    fn take_data(&mut self) -> Option<String> {
        mem::take(&mut self.data)
    }
}
```

### Swap Optimization
```rust
fn rotate_left(arr: &mut [i32]) {
    if arr.len() < 2 { return; }
    let first = mem::take(&mut arr[0]);
    for i in 1..arr.len() {
        arr[i - 1] = mem::take(&mut arr[i]);
    }
    *arr.last_mut().unwrap() = first;
}
```

## Unsafe Operations

```rust
// transmute - reinterpret bits (VERY UNSAFE)
let x: u32 = 42;
let y: f32 = unsafe { mem::transmute(x) };

// zeroed - all zeros (UNSAFE for most types)
let x: i32 = unsafe { mem::zeroed() };  // OK for i32
// NEVER use zeroed for types with invariants (Box, &T, etc.)
```
