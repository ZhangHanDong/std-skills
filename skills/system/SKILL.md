---
name: rust-std-system
description: |
  CRITICAL: Use for Rust system interaction. Triggers on:
  std::fs, std::path, std::env, std::process, std::net, std::time,
  File, Path, PathBuf, Command, TcpListener, TcpStream, UdpSocket,
  Duration, Instant, SystemTime, read_to_string, create_dir,
  文件系统, 路径, 环境变量, 进程, 网络, 时间, 文件操作
---

# Rust System Interaction

> **Version:** 1.92.0 | **Last Updated:** 2026-01-19
>
> Source: https://doc.rust-lang.org/std/

Help users with filesystem, paths, environment, processes, networking, and time operations.

## Documentation

Refer to the local files for detailed documentation:
- `../../references/system/fs.md` - Filesystem operations
- `../../references/system/path.md` - Path and PathBuf
- `../../references/system/env.md` - Environment variables
- `../../references/system/process.md` - Process spawning
- `../../references/system/net.md` - TCP/UDP networking
- `../../references/system/time.md` - Duration, Instant, SystemTime
- `../../references/_shared/rust-defaults.md` - Rust code generation defaults

## Module Overview

| Module | Key Types | Purpose |
|--------|-----------|---------|
| `std::fs` | File, Metadata, DirEntry | Filesystem operations |
| `std::path` | Path, PathBuf | Path manipulation |
| `std::env` | - | Environment & args |
| `std::process` | Command, Child, Output | Spawn processes |
| `std::net` | TcpListener, TcpStream, UdpSocket | Networking |
| `std::time` | Duration, Instant, SystemTime | Time operations |

## Key Patterns

### File Operations
```rust
use std::fs;

// Read entire file
let content = fs::read_to_string("file.txt")?;
let bytes = fs::read("file.bin")?;

// Write file
fs::write("file.txt", "content")?;

// Directory operations
fs::create_dir_all("path/to/dir")?;
for entry in fs::read_dir(".")? {
    let entry = entry?;
    println!("{:?}", entry.path());
}
```

### Path Operations
```rust
use std::path::{Path, PathBuf};

let path = Path::new("/tmp/foo/bar.txt");
let parent = path.parent();       // Some("/tmp/foo")
let name = path.file_name();      // Some("bar.txt")
let ext = path.extension();       // Some("txt")

// Build paths
let mut path = PathBuf::from("/tmp");
path.push("subdir");
path.push("file.txt");
```

### Environment
```rust
use std::env;

// Get env variable
let home = env::var("HOME")?;

// Command line args
for arg in env::args().skip(1) {
    println!("{}", arg);
}

// Current directory
let cwd = env::current_dir()?;
```

### Process Spawning
```rust
use std::process::Command;

// Simple command
let output = Command::new("ls")
    .args(["-l", "-a"])
    .output()?;

println!("{}", String::from_utf8_lossy(&output.stdout));

// With piped I/O
use std::process::Stdio;
let mut child = Command::new("cat")
    .stdin(Stdio::piped())
    .stdout(Stdio::piped())
    .spawn()?;
```

### TCP Server
```rust
use std::net::TcpListener;
use std::io::{Read, Write};

let listener = TcpListener::bind("127.0.0.1:8080")?;
for stream in listener.incoming() {
    let mut stream = stream?;
    let mut buf = [0; 1024];
    let n = stream.read(&mut buf)?;
    stream.write_all(&buf[..n])?;
}
```

### Timing
```rust
use std::time::{Duration, Instant};

let start = Instant::now();
expensive_operation();
println!("Took: {:?}", start.elapsed());

// Timeouts
let timeout = Duration::from_secs(30);
```

## Quick Reference Tables

### std::fs Functions
| Function | Description |
|----------|-------------|
| `read(path)` | Read file to `Vec<u8>` |
| `read_to_string(path)` | Read file to `String` |
| `write(path, data)` | Write to file |
| `copy(from, to)` | Copy file |
| `rename(from, to)` | Move/rename |
| `remove_file(path)` | Delete file |
| `create_dir(path)` | Create directory |
| `create_dir_all(path)` | Create dirs recursively |
| `remove_dir_all(path)` | Delete dir recursively |
| `metadata(path)` | Get file info |

### Path Methods
| Method | Description |
|--------|-------------|
| `parent()` | Parent directory |
| `file_name()` | File name |
| `extension()` | File extension |
| `join(path)` | Join paths |
| `push(path)` | Append to PathBuf |
| `exists()` | Check existence |
| `is_file()` / `is_dir()` | Check type |

### Duration Creation
| Method | Example |
|--------|---------|
| `from_secs(n)` | 5 seconds |
| `from_millis(n)` | 500 ms |
| `from_micros(n)` | 1000 μs |
| `from_nanos(n)` | 1_000_000 ns |

## When to Use Which

- **Path vs PathBuf**: `Path` for borrowing, `PathBuf` for owning
- **Instant vs SystemTime**: `Instant` for timing, `SystemTime` for timestamps
- **read_to_string vs File**: Simple reads use function, complex I/O use File
- **Command::output vs spawn**: `output()` waits, `spawn()` runs async

## When Writing Code

1. Use `?` for error propagation in I/O operations
2. Prefer `PathBuf::push()` over string concatenation
3. Use `create_dir_all()` instead of checking existence first
4. Always handle `Command` output status codes
5. Use `Instant` for timing, not `SystemTime`
6. Consider TOCTOU vulnerabilities in filesystem code
