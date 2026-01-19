# File Operations Reference

## Quick Operations (std::fs)

| Function | Description |
|----------|-------------|
| `read(path)` | Read file to `Vec<u8>` |
| `read_to_string(path)` | Read file to String |
| `write(path, data)` | Write data to file |
| `copy(from, to)` | Copy file |
| `rename(from, to)` | Move/rename file |
| `remove_file(path)` | Delete file |
| `create_dir(path)` | Create directory |
| `create_dir_all(path)` | Create directory and parents |
| `remove_dir(path)` | Delete empty directory |
| `remove_dir_all(path)` | Delete directory recursively |
| `read_dir(path)` | List directory |
| `metadata(path)` | Get file metadata |

## File Type

### Opening Files

```rust
use std::fs::File;

// Read only
let file = File::open("file.txt")?;

// Write (create or truncate)
let file = File::create("file.txt")?;

// With options
use std::fs::OpenOptions;

let file = OpenOptions::new()
    .read(true)
    .write(true)
    .create(true)
    .append(false)
    .truncate(false)
    .open("file.txt")?;
```

### OpenOptions Flags

| Method | Description |
|--------|-------------|
| `read(true)` | Enable reading |
| `write(true)` | Enable writing |
| `append(true)` | Append mode |
| `truncate(true)` | Truncate to 0 bytes |
| `create(true)` | Create if not exists |
| `create_new(true)` | Create, fail if exists |

## Metadata

```rust
use std::fs;

let meta = fs::metadata("file.txt")?;

meta.len()           // File size in bytes
meta.is_file()       // Is regular file
meta.is_dir()        // Is directory
meta.is_symlink()    // Is symbolic link
meta.modified()?     // Last modification time
meta.accessed()?     // Last access time
meta.created()?      // Creation time (if available)
meta.permissions()   // File permissions
```

## Directory Iteration

```rust
use std::fs;

for entry in fs::read_dir(".")? {
    let entry = entry?;
    let path = entry.path();
    let name = entry.file_name();
    let meta = entry.metadata()?;

    if meta.is_file() {
        println!("File: {:?}", path);
    }
}

// Recursive with walkdir crate (recommended)
// use walkdir::WalkDir;
// for entry in WalkDir::new(".") { }
```

## Path Operations

```rust
use std::path::{Path, PathBuf};

let path = Path::new("/home/user/file.txt");

path.exists()
path.is_file()
path.is_dir()
path.extension()        // Some("txt")
path.file_name()        // Some("file.txt")
path.file_stem()        // Some("file")
path.parent()           // Some("/home/user")
path.join("subdir")     // /home/user/file.txt/subdir

// Mutable path
let mut path = PathBuf::from("/home");
path.push("user");
path.push("file.txt");
path.set_extension("md");
```

## Permissions (Unix)

```rust
#[cfg(unix)]
{
    use std::os::unix::fs::PermissionsExt;

    let meta = fs::metadata("file.txt")?;
    let mode = meta.permissions().mode();

    // Set permissions
    let mut perms = meta.permissions();
    perms.set_mode(0o755);
    fs::set_permissions("file.txt", perms)?;
}
```

## Temporary Files

```rust
use std::env;
use std::fs::File;

let temp_dir = env::temp_dir();
let temp_file = temp_dir.join("my_temp_file.txt");
let file = File::create(&temp_file)?;

// Better: use tempfile crate
// use tempfile::NamedTempFile;
// let file = NamedTempFile::new()?;
```
