# Filesystem (std::fs) Reference

## Key Structs

| Struct | Purpose |
|--------|---------|
| `File` | Open file handle |
| `Metadata` | File information |
| `DirEntry` | Directory entry |
| `OpenOptions` | File open configuration |
| `Permissions` | File permissions |
| `ReadDir` | Directory iterator |

## File Operations

### Reading Files
```rust
use std::fs;

// Read entire file to String
let contents = fs::read_to_string("file.txt")?;

// Read entire file to bytes
let bytes = fs::read("file.bin")?;

// Read with File handle
use std::io::Read;
let mut file = fs::File::open("file.txt")?;
let mut contents = String::new();
file.read_to_string(&mut contents)?;
```

### Writing Files
```rust
use std::fs;

// Write entire contents
fs::write("file.txt", "content")?;

// Write with File handle
use std::io::Write;
let mut file = fs::File::create("file.txt")?;
file.write_all(b"content")?;

// Append mode
use std::fs::OpenOptions;
let mut file = OpenOptions::new()
    .append(true)
    .open("file.txt")?;
```

### File Metadata
```rust
let meta = fs::metadata("file.txt")?;
meta.len()           // File size in bytes
meta.is_file()       // Is regular file
meta.is_dir()        // Is directory
meta.is_symlink()    // Is symlink
meta.modified()?     // Last modification time
meta.permissions()   // File permissions
```

## Directory Operations

```rust
use std::fs;

// Create directory
fs::create_dir("new_dir")?;
fs::create_dir_all("path/to/new_dir")?;  // Recursive

// Remove directory
fs::remove_dir("empty_dir")?;
fs::remove_dir_all("dir_with_contents")?;

// Iterate directory
for entry in fs::read_dir(".")? {
    let entry = entry?;
    let path = entry.path();
    let name = entry.file_name();
    let meta = entry.metadata()?;
}
```

## Common Functions

| Function | Description |
|----------|-------------|
| `read(path)` | Read file to `Vec<u8>` |
| `read_to_string(path)` | Read file to `String` |
| `write(path, data)` | Write data to file |
| `copy(from, to)` | Copy file |
| `rename(from, to)` | Move/rename file |
| `remove_file(path)` | Delete file |
| `create_dir(path)` | Create directory |
| `create_dir_all(path)` | Create directories recursively |
| `remove_dir(path)` | Remove empty directory |
| `remove_dir_all(path)` | Remove directory recursively |
| `metadata(path)` | Get file metadata |
| `canonicalize(path)` | Get absolute path |
| `exists(path)` | Check if path exists |

## OpenOptions Builder

```rust
use std::fs::OpenOptions;

let file = OpenOptions::new()
    .read(true)
    .write(true)
    .create(true)
    .truncate(true)
    .append(false)
    .open("file.txt")?;
```

## Security: TOCTOU Warning

Time-of-Check to Time-of-Use race condition:

```rust
// UNSAFE: File could change between check and use
if path.exists() {
    fs::remove_file(path)?;
}

// SAFE: Atomic operation
fs::File::create_new("file.txt")?;  // Fails if exists
```

Use atomic operations when possible.
