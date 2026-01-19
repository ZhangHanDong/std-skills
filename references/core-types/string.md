# String Reference

## String vs &str

| Type | Ownership | Mutability | Storage |
|------|-----------|------------|---------|
| `String` | Owned | Mutable | Heap |
| `&str` | Borrowed | Immutable | Stack/Heap/Static |

## Creation

```rust
// From literal
let s = String::from("hello");
let s = "hello".to_string();
let s: String = "hello".into();

// Empty
let s = String::new();
let s = String::with_capacity(100);

// From bytes (UTF-8)
let s = String::from_utf8(vec![104, 101, 108, 108, 111])?;
let s = String::from_utf8_lossy(&bytes);  // Replaces invalid UTF-8
```

## Common Methods

| Method | Description | Returns |
|--------|-------------|---------|
| `push(char)` | Append char | `()` |
| `push_str(&str)` | Append string | `()` |
| `pop()` | Remove last char | `Option<char>` |
| `insert(idx, char)` | Insert at byte index | `()` |
| `remove(idx)` | Remove at byte index | `char` |
| `clear()` | Empty the string | `()` |
| `len()` | Byte length | `usize` |
| `is_empty()` | Check if empty | `bool` |
| `capacity()` | Allocated capacity | `usize` |

## Slicing

```rust
let s = String::from("hello");
let slice: &str = &s;         // Whole string
let slice: &str = &s[0..2];   // "he" (byte indices!)

// Safe: char boundaries
let hello = "Здравствуйте";
let s = &hello[0..4];  // "Зд" (2 chars, 4 bytes)
// Panic: &hello[0..1] - not a char boundary!
```

## Iteration

```rust
// By char
for c in s.chars() { }

// By byte
for b in s.bytes() { }

// By grapheme cluster (needs unicode-segmentation crate)
```

## Concatenation

```rust
// + operator (takes ownership of left)
let s = s1 + &s2;

// format! macro
let s = format!("{} {}", s1, s2);

// push_str
s1.push_str(&s2);
```

## Conversion

```rust
// String <-> &str
let slice: &str = &string;
let owned: String = slice.to_string();

// String <-> Vec<u8>
let bytes: Vec<u8> = string.into_bytes();
let string = String::from_utf8(bytes)?;

// Parse
let num: i32 = "42".parse()?;
let num: i32 = "42".parse::<i32>()?;
```

## Searching

```rust
s.contains("pattern")
s.starts_with("pre")
s.ends_with("suf")
s.find("pattern")      // Option<usize> (byte index)
s.rfind("pattern")     // From end
s.matches("pat")       // Iterator of matches
```

## Transformation

```rust
s.to_lowercase()
s.to_uppercase()
s.trim()
s.trim_start()
s.trim_end()
s.replace("old", "new")
s.split(' ')           // Iterator
s.split_whitespace()
s.lines()
```

## Performance Tips

- Use `&str` for function parameters when possible
- Use `String::with_capacity()` for known sizes
- Use `push_str()` instead of `+` for multiple concatenations
- `format!` is convenient but allocates
