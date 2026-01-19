# Environment (std::env) Reference

## Environment Variables

### Reading
```rust
use std::env;

// Returns Result<String, VarError>
let value = env::var("HOME")?;

// Returns Option<OsString> (for non-UTF8 values)
let value = env::var_os("HOME");

// Iterate all variables
for (key, value) in env::vars() {
    println!("{}={}", key, value);
}
```

### Setting
```rust
use std::env;

env::set_var("MY_VAR", "value");
env::remove_var("MY_VAR");
```

**Warning:** Setting environment variables is not thread-safe on some platforms.

## Current Directory

```rust
use std::env;

// Get current directory
let cwd = env::current_dir()?;

// Change directory
env::set_current_dir("/tmp")?;
```

## Command Line Arguments

```rust
use std::env;

// Skip first (program name)
for arg in env::args().skip(1) {
    println!("{}", arg);
}

// Collect to Vec
let args: Vec<String> = env::args().collect();

// For non-UTF8 args
let args: Vec<OsString> = env::args_os().collect();
```

## Common Functions

| Function | Returns | Description |
|----------|---------|-------------|
| `var(key)` | `Result<String>` | Get env variable |
| `var_os(key)` | `Option<OsString>` | Get env (non-UTF8 safe) |
| `set_var(key, val)` | `()` | Set env variable |
| `remove_var(key)` | `()` | Remove env variable |
| `vars()` | `Vars` | Iterator of (key, value) |
| `current_dir()` | `Result<PathBuf>` | Current working directory |
| `set_current_dir(path)` | `Result<()>` | Change directory |
| `current_exe()` | `Result<PathBuf>` | Path to current executable |
| `temp_dir()` | `PathBuf` | System temp directory |
| `home_dir()` | `Option<PathBuf>` | User home directory (deprecated) |
| `args()` | `Args` | Command line args iterator |
| `args_os()` | `ArgsOs` | Args as OsString |

## PATH Manipulation

```rust
use std::env;

// Parse PATH variable
let paths = env::var_os("PATH")
    .map(|p| env::split_paths(&p).collect::<Vec<_>>());

// Join paths
let new_path = env::join_paths(["/usr/bin", "/usr/local/bin"])?;
```

## Common Patterns

### CLI Argument Parsing
```rust
let args: Vec<String> = env::args().collect();
let program = &args[0];
let input = args.get(1).expect("missing input file");
```

### Config from Environment
```rust
let port: u16 = env::var("PORT")
    .unwrap_or_else(|_| "8080".to_string())
    .parse()
    .expect("PORT must be a number");
```
