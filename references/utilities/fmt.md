# Formatting (std::fmt) Reference

## Display vs Debug

| Trait | Format | Purpose |
|-------|--------|---------|
| `Display` | `{}` | User-facing output |
| `Debug` | `{:?}` | Developer debugging |

## Format Specifiers

| Specifier | Trait | Example |
|-----------|-------|---------|
| `{}` | Display | `"hello"` |
| `{:?}` | Debug | `"hello"` |
| `{:#?}` | Debug (pretty) | Multi-line |
| `{:x}` | LowerHex | `1a` |
| `{:X}` | UpperHex | `1A` |
| `{:b}` | Binary | `101010` |
| `{:o}` | Octal | `52` |
| `{:e}` | LowerExp | `1.5e2` |
| `{:E}` | UpperExp | `1.5E2` |
| `{:p}` | Pointer | `0x7ff...` |

## Width and Alignment

```rust
format!("{:10}", "hi")     // "hi        " (default left)
format!("{:>10}", "hi")    // "        hi" (right)
format!("{:^10}", "hi")    // "    hi    " (center)
format!("{:-<10}", "hi")   // "hi--------" (custom fill)
format!("{:0>5}", 42)      // "00042" (zero padding)
```

## Precision

```rust
format!("{:.2}", 3.14159)  // "3.14" (2 decimals)
format!("{:.5}", "hello world")  // "hello" (max chars)
format!("{:10.2}", 3.14)   // "      3.14" (width + precision)
```

## Sign and Alternate Forms

```rust
format!("{:+}", 5)         // "+5" (always show sign)
format!("{:#x}", 255)      // "0xff" (prefix for hex)
format!("{:#b}", 10)       // "0b1010" (prefix for binary)
format!("{:#?}", obj)      // Pretty-printed debug
```

## Implementing Display

```rust
use std::fmt;

struct Point {
    x: i32,
    y: i32,
}

impl fmt::Display for Point {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "({}, {})", self.x, self.y)
    }
}

let p = Point { x: 3, y: 4 };
println!("{}", p);  // "(3, 4)"
```

## Implementing Debug

```rust
use std::fmt;

struct Point { x: i32, y: i32 }

// Manual implementation
impl fmt::Debug for Point {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        f.debug_struct("Point")
            .field("x", &self.x)
            .field("y", &self.y)
            .finish()
    }
}

// Or just derive (recommended)
#[derive(Debug)]
struct Point2 { x: i32, y: i32 }
```

## Debug Helpers

```rust
use std::fmt;

impl fmt::Debug for MyStruct {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        // For structs
        f.debug_struct("MyStruct")
            .field("name", &self.name)
            .finish()

        // For tuples
        f.debug_tuple("MyTuple")
            .field(&self.0)
            .finish()

        // For lists
        f.debug_list()
            .entries(self.items.iter())
            .finish()

        // For maps
        f.debug_map()
            .entries(self.map.iter())
            .finish()

        // For sets
        f.debug_set()
            .entries(self.set.iter())
            .finish()
    }
}
```

## Formatter Methods

```rust
impl fmt::Display for MyType {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        // Get formatting options
        let width = f.width();           // Option<usize>
        let precision = f.precision();   // Option<usize>
        let fill = f.fill();             // char
        let align = f.align();           // Option<Alignment>

        // Pad output
        f.pad("content")?;

        // Write directly
        write!(f, "{}", self.value)
    }
}
```

## Named Arguments

```rust
format!("{name} is {age}", name = "Alice", age = 30);
format!("{0} and {1} and {0}", "a", "b");  // Positional
```

## Common Macros

| Macro | Output |
|-------|--------|
| `format!` | `String` |
| `print!` / `println!` | stdout |
| `eprint!` / `eprintln!` | stderr |
| `write!` / `writeln!` | to `fmt::Write` |
| `format_args!` | `Arguments` (no alloc) |
