# Error (std::error) Reference

## Error Trait

```rust
pub trait Error: Debug + Display {
    fn source(&self) -> Option<&(dyn Error + 'static)> { None }
}
```

## Implementing Error

### Basic Implementation
```rust
use std::error::Error;
use std::fmt;

#[derive(Debug)]
struct MyError {
    message: String,
}

impl fmt::Display for MyError {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "{}", self.message)
    }
}

impl Error for MyError {}
```

### With Source (Cause)
```rust
use std::error::Error;
use std::fmt;
use std::io;

#[derive(Debug)]
struct FileError {
    path: String,
    source: io::Error,
}

impl fmt::Display for FileError {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "error reading {}", self.path)
    }
}

impl Error for FileError {
    fn source(&self) -> Option<&(dyn Error + 'static)> {
        Some(&self.source)
    }
}
```

### Using thiserror (Recommended)
```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum MyError {
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),

    #[error("Parse error at line {line}: {message}")]
    Parse { line: usize, message: String },

    #[error("Not found: {0}")]
    NotFound(String),
}
```

## Error Chain

```rust
fn print_error_chain(err: &dyn Error) {
    eprintln!("Error: {}", err);
    let mut source = err.source();
    while let Some(cause) = source {
        eprintln!("Caused by: {}", cause);
        source = cause.source();
    }
}
```

## Box<dyn Error>

```rust
use std::error::Error;

type Result<T> = std::result::Result<T, Box<dyn Error>>;

fn fallible() -> Result<()> {
    let file = std::fs::read_to_string("file.txt")?;
    let num: i32 = file.trim().parse()?;
    Ok(())
}
```

## anyhow (Recommended for Applications)

```rust
use anyhow::{Context, Result};

fn read_config() -> Result<Config> {
    let content = std::fs::read_to_string("config.json")
        .context("failed to read config file")?;

    let config: Config = serde_json::from_str(&content)
        .context("failed to parse config")?;

    Ok(config)
}
```

## Downcasting

```rust
use std::error::Error;
use std::io;

fn handle_error(err: &dyn Error) {
    if let Some(io_err) = err.downcast_ref::<io::Error>() {
        match io_err.kind() {
            io::ErrorKind::NotFound => println!("File not found"),
            io::ErrorKind::PermissionDenied => println!("Access denied"),
            _ => println!("IO error: {}", io_err),
        }
    }
}
```

## Best Practices

1. **Libraries**: Use `thiserror` for custom error types
2. **Applications**: Use `anyhow` for error propagation
3. Always implement `source()` when wrapping errors
4. Provide context with `.context()` (anyhow) or custom Display
5. Use `?` operator for error propagation
6. Never use `unwrap()` in library code

## Error vs Panic

| Use | For |
|-----|-----|
| `Result` + `Error` | Expected failures (file not found, parse error) |
| `panic!` | Bugs, invariant violations, unrecoverable state |
| `unwrap()` | Tests, examples, when failure is impossible |
| `expect()` | When panic message helps debugging |
