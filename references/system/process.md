# Process (std::process) Reference

## Command Builder

```rust
use std::process::Command;

// Simple command
let output = Command::new("echo")
    .arg("hello")
    .output()?;

// Multiple args
let output = Command::new("ls")
    .args(["-l", "-a"])
    .output()?;
```

## Spawning Processes

### output() - Wait for completion
```rust
let output = Command::new("ls").output()?;

println!("stdout: {}", String::from_utf8_lossy(&output.stdout));
println!("stderr: {}", String::from_utf8_lossy(&output.stderr));
println!("exit code: {:?}", output.status.code());
```

### spawn() - Manual control
```rust
let mut child = Command::new("sleep")
    .arg("5")
    .spawn()?;

// Do other work...

let status = child.wait()?;  // Wait for completion
```

## I/O Configuration

```rust
use std::process::{Command, Stdio};

let child = Command::new("cat")
    .stdin(Stdio::piped())
    .stdout(Stdio::piped())
    .stderr(Stdio::null())
    .spawn()?;
```

| Stdio | Description |
|-------|-------------|
| `Stdio::inherit()` | Use parent's I/O |
| `Stdio::piped()` | Create pipe |
| `Stdio::null()` | Discard output |

## Piping Data

```rust
use std::process::{Command, Stdio};
use std::io::Write;

let mut child = Command::new("cat")
    .stdin(Stdio::piped())
    .stdout(Stdio::piped())
    .spawn()?;

// Write to stdin
if let Some(mut stdin) = child.stdin.take() {
    stdin.write_all(b"Hello")?;
}

let output = child.wait_with_output()?;
```

## Pipe Between Commands

```rust
let echo = Command::new("echo")
    .arg("hello world")
    .stdout(Stdio::piped())
    .spawn()?;

let grep = Command::new("grep")
    .arg("hello")
    .stdin(echo.stdout.unwrap())
    .output()?;
```

## Environment and Directory

```rust
Command::new("app")
    .env("KEY", "value")           // Set env variable
    .env_clear()                   // Clear all env
    .env_remove("KEY")             // Remove variable
    .current_dir("/tmp")           // Working directory
    .spawn()?;
```

## Exit Handling

```rust
use std::process;

// Exit current process
process::exit(0);

// Abort (abnormal termination)
process::abort();

// Get current process ID
let pid = process::id();
```

## Output Struct

```rust
pub struct Output {
    pub status: ExitStatus,
    pub stdout: Vec<u8>,
    pub stderr: Vec<u8>,
}

// Check success
if output.status.success() {
    // ...
}

// Get exit code
let code: Option<i32> = output.status.code();
```

## Common Patterns

### Check Command Exists
```rust
let exists = Command::new("which")
    .arg("git")
    .output()
    .map(|o| o.status.success())
    .unwrap_or(false);
```

### Run Shell Command
```rust
#[cfg(unix)]
let output = Command::new("sh")
    .args(["-c", "echo $HOME"])
    .output()?;

#[cfg(windows)]
let output = Command::new("cmd")
    .args(["/C", "echo %USERPROFILE%"])
    .output()?;
```
