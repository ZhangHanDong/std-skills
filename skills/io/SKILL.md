---
name: rust-std-io
description: |
  CRITICAL: Use for Rust std I/O. Triggers on:
  Read, Write, BufRead, BufReader, BufWriter, File, stdin, stdout, stderr,
  io::Result, io::Error, io::copy, io::Cursor, seek, 读写, 文件, 输入输出
---

# Rust Standard I/O

> **Version:** 1.92.0 | **Last Updated:** 2026-01-19
>
> Source: https://doc.rust-lang.org/std/io/

Help users with Rust standard library I/O operations.

## Documentation

Refer to the local files for detailed documentation:
- `../../references/io/traits.md` - Read/Write traits
- `../../references/io/file-ops.md` - File operations
- `../../references/_shared/rust-defaults.md` - Rust code generation defaults

## I/O Traits

| Trait | Purpose | Key Methods |
|-------|---------|-------------|
| `Read` | Read bytes | `read()`, `read_to_end()`, `read_to_string()` |
| `Write` | Write bytes | `write()`, `write_all()`, `flush()` |
| `BufRead` | Buffered read | `read_line()`, `lines()`, `fill_buf()` |
| `Seek` | Random access | `seek()`, `rewind()`, `stream_position()` |

## Key Patterns

### Read File to String
```rust
use std::fs;

// Simple way
let contents = fs::read_to_string("file.txt")?;

// With File
use std::io::Read;
use std::fs::File;

let mut file = File::open("file.txt")?;
let mut contents = String::new();
file.read_to_string(&mut contents)?;
```

### Write to File
```rust
use std::fs;

// Simple way
fs::write("file.txt", "content")?;

// With File
use std::io::Write;
use std::fs::File;

let mut file = File::create("file.txt")?;
file.write_all(b"content")?;
```

### Buffered Reading (Line by Line)
```rust
use std::io::{BufRead, BufReader};
use std::fs::File;

let file = File::open("file.txt")?;
let reader = BufReader::new(file);

for line in reader.lines() {
    let line = line?;
    println!("{}", line);
}
```

### Buffered Writing
```rust
use std::io::{BufWriter, Write};
use std::fs::File;

let file = File::create("file.txt")?;
let mut writer = BufWriter::new(file);

writer.write_all(b"line 1\n")?;
writer.write_all(b"line 2\n")?;
writer.flush()?;
```

### Standard I/O
```rust
use std::io::{self, Write, BufRead};

// Read line from stdin
let stdin = io::stdin();
let mut input = String::new();
stdin.read_line(&mut input)?;

// Buffered stdin
let stdin = io::stdin();
for line in stdin.lock().lines() {
    let line = line?;
    println!("Got: {}", line);
}

// Write to stdout
let stdout = io::stdout();
let mut handle = stdout.lock();
writeln!(handle, "output")?;

// Write to stderr
let stderr = io::stderr();
writeln!(stderr.lock(), "error")?;
```

### Copy Between Readers/Writers
```rust
use std::io;

let mut reader: &[u8] = b"data";
let mut writer = Vec::new();
io::copy(&mut reader, &mut writer)?;
```

### Cursor (In-Memory I/O)
```rust
use std::io::{Cursor, Read, Seek, SeekFrom};

let data = b"hello world";
let mut cursor = Cursor::new(data);

let mut buf = [0; 5];
cursor.read_exact(&mut buf)?;  // "hello"

cursor.seek(SeekFrom::Start(6))?;
cursor.read_exact(&mut buf)?;  // "world"
```

### File Metadata and Permissions
```rust
use std::fs;

let metadata = fs::metadata("file.txt")?;
println!("Size: {} bytes", metadata.len());
println!("Is file: {}", metadata.is_file());
println!("Is dir: {}", metadata.is_dir());
println!("Modified: {:?}", metadata.modified()?);

// Set permissions (Unix)
#[cfg(unix)]
{
    use std::os::unix::fs::PermissionsExt;
    let mut perms = metadata.permissions();
    perms.set_mode(0o755);
    fs::set_permissions("file.txt", perms)?;
}
```

### Directory Operations
```rust
use std::fs;

// Create directory
fs::create_dir("new_dir")?;
fs::create_dir_all("path/to/dir")?;

// Read directory
for entry in fs::read_dir(".")? {
    let entry = entry?;
    println!("{:?}", entry.path());
}

// Remove
fs::remove_file("file.txt")?;
fs::remove_dir("empty_dir")?;
fs::remove_dir_all("dir_with_contents")?;
```

## Error Handling

```rust
use std::io;

fn read_file() -> io::Result<String> {
    let contents = std::fs::read_to_string("file.txt")?;
    Ok(contents)
}

// Custom error handling
match std::fs::read_to_string("file.txt") {
    Ok(contents) => println!("{}", contents),
    Err(e) if e.kind() == io::ErrorKind::NotFound => {
        println!("File not found");
    }
    Err(e) => return Err(e),
}
```

## API Reference Table

| Function | Purpose |
|----------|---------|
| `fs::read(path)` | Read file to `Vec<u8>` |
| `fs::read_to_string(path)` | Read file to String |
| `fs::write(path, data)` | Write data to file |
| `fs::copy(from, to)` | Copy file |
| `fs::rename(from, to)` | Move/rename file |
| `fs::remove_file(path)` | Delete file |
| `fs::create_dir(path)` | Create directory |
| `fs::read_dir(path)` | List directory |
| `File::open(path)` | Open for reading |
| `File::create(path)` | Create/truncate for writing |

## When Writing Code

1. Use `BufReader`/`BufWriter` for many small reads/writes
2. Always handle `io::Error` with `?` or match
3. Use `fs::read_to_string` for simple file reading
4. Lock stdin/stdout/stderr for repeated operations
5. Remember to `flush()` buffered writers
6. Use `io::copy` for efficient byte copying

## When Answering Questions

1. Explain buffering benefits for performance
2. Mention platform-specific behavior differences
3. Suggest appropriate error handling patterns
4. Reference `std::path::Path` for path manipulation
