# Path (std::path) Reference

## Path vs PathBuf

| Type | Analogy | Ownership | Mutability |
|------|---------|-----------|------------|
| `Path` | `str` | Borrowed | Immutable |
| `PathBuf` | `String` | Owned | Mutable |

## Creating Paths

```rust
use std::path::{Path, PathBuf};

// Path (borrowed)
let path = Path::new("/tmp/foo.txt");

// PathBuf (owned)
let mut path = PathBuf::new();
path.push("/tmp");
path.push("foo.txt");

// From string
let path = PathBuf::from("/tmp/foo.txt");

// From iterator
let path: PathBuf = ["/tmp", "foo", "bar.txt"].iter().collect();
```

## Query Methods (Path)

```rust
let path = Path::new("/tmp/foo/bar.txt");

path.parent()        // Some(Path::new("/tmp/foo"))
path.file_name()     // Some(OsStr::new("bar.txt"))
path.file_stem()     // Some(OsStr::new("bar"))
path.extension()     // Some(OsStr::new("txt"))

path.is_absolute()   // true
path.is_relative()   // false
path.exists()        // Check filesystem
path.is_file()       // Check if file
path.is_dir()        // Check if directory

path.starts_with("/tmp")
path.ends_with("bar.txt")
```

## Modification Methods (PathBuf)

```rust
let mut path = PathBuf::from("/tmp/foo");

path.push("bar.txt");          // /tmp/foo/bar.txt
path.pop();                    // /tmp/foo
path.set_file_name("baz.txt"); // /tmp/baz.txt
path.set_extension("md");      // /tmp/baz.md
```

## Path Components

```rust
use std::path::{Component, Path};

let path = Path::new("/tmp/../foo/./bar.txt");

for component in path.components() {
    match component {
        Component::RootDir => println!("root"),
        Component::Normal(name) => println!("{:?}", name),
        Component::ParentDir => println!(".."),
        Component::CurDir => println!("."),
        Component::Prefix(p) => println!("prefix: {:?}", p),
    }
}
```

## Common Operations

```rust
// Join paths
let full = path.join("subdir").join("file.txt");

// Strip prefix
let relative = path.strip_prefix("/tmp")?;

// Iterate ancestors
for ancestor in path.ancestors() {
    println!("{:?}", ancestor);
}

// Canonicalize (resolve symlinks, make absolute)
let absolute = path.canonicalize()?;
```

## Conversion

```rust
// Path <-> PathBuf
let path_buf: PathBuf = path.to_path_buf();
let path: &Path = &path_buf;

// To string (may fail for non-UTF8)
let s: Option<&str> = path.to_str();
let s: String = path.to_string_lossy().into_owned();

// To OsStr
let os: &OsStr = path.as_os_str();
```

## Cross-Platform Notes

- `/` works as separator on all platforms
- Windows also accepts `\`
- Case sensitivity varies by platform
- Use `Path` methods for portable code
