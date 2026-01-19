# Rust Std Skills

[中文](README-zh.md) | [日本語](README-ja.md)

> Comprehensive skills collection for Rust standard library.

## Structure

```
rust-std-skills/
├── SKILL.md              # Main entry point (overview + quick reference)
├── skills/               # Sub-module skills
│   ├── core-types/      # Option, Result, String, Box, Rc, Cell
│   ├── collections/     # Vec, HashMap, HashSet, BTreeMap, etc.
│   ├── sync/            # Arc, Mutex, RwLock, Atomic*, thread, channels
│   ├── traits/          # Iterator, From/Into, Clone, Debug, etc.
│   ├── io/              # Read, Write, BufReader, File, stdin/stdout
│   ├── system/          # fs, path, env, process, net, time
│   └── utilities/       # mem, fmt, error, marker traits
└── references/          # Detailed documentation
    ├── _shared/         # Shared rules (rust-defaults.md)
    ├── core-types/      # Option, Result, String, Box, Rc, Cell
    ├── collections/     # Vec, HashMap references
    ├── sync/            # Mutex, Arc, thread, mpsc, atomics
    ├── traits/          # Derivable, conversion traits
    ├── io/              # I/O traits, file operations
    ├── system/          # fs, path, env, process, net, time
    └── utilities/       # mem, fmt, error, marker
```

## Usage

### As Claude Code Skills

Copy or symlink this directory to your Claude Code skills directory:

```bash
# Option 1: Symlink
ln -s /path/to/rust-std-skills ~/.claude/skills/rust-std

# Option 2: Copy
cp -r /path/to/rust-std-skills ~/.claude/skills/rust-std
```

### Trigger Keywords

The skill activates on keywords like:
- `std::`, `standard library`, `rust std`
- `Option`, `Result`, `String`, `Box`, `Rc`, `Cell`, `RefCell`
- `Vec`, `HashMap`, `HashSet`, `BTreeMap`
- `Arc`, `Mutex`, `RwLock`, `Atomic*`, `mpsc`, `thread`
- `Iterator`, `From`, `Into`, `Clone`, `Debug`, `Display`
- `Read`, `Write`, `File`, `BufReader`, `Path`, `PathBuf`
- `fs`, `env`, `process`, `Command`, `TcpListener`, `Duration`
- `Send`, `Sync`, `Copy`, `Drop`, `Sized`, `PhantomData`
- `mem::swap`, `mem::take`, `format!`, `Error`

## Modules

| Module | Purpose | Key Types |
|--------|---------|-----------|
| **core-types** | Fundamental types | `Option`, `Result`, `String`, `Box`, `Rc`, `Cell` |
| **collections** | Data structures | `Vec`, `HashMap`, `HashSet`, `BTreeMap`, `VecDeque` |
| **sync** | Concurrency | `Arc`, `Mutex`, `RwLock`, `Atomic*`, `thread`, `mpsc` |
| **traits** | Core traits | `Iterator`, `From/Into`, `Clone`, `Debug`, `Default` |
| **io** | Input/Output | `Read`, `Write`, `BufReader`, `File`, `stdin/stdout` |
| **system** | OS interaction | `fs`, `path`, `env`, `process`, `net`, `time` |
| **utilities** | Helper modules | `mem`, `fmt`, `error`, `marker` |

## Quick Reference

### Core Types
- `Option<T>` - Optional value (Some/None)
- `Result<T, E>` - Success/error result
- `String` / `&str` - UTF-8 strings
- `Box<T>` - Heap allocation
- `Rc<T>` / `Arc<T>` - Reference counting
- `Cell<T>` / `RefCell<T>` - Interior mutability

### Collections
- `Vec<T>` - Growable array
- `HashMap<K, V>` - Hash-based key-value map
- `HashSet<T>` - Unique values
- `BTreeMap<K, V>` - Sorted map

### Concurrency
- `Arc<Mutex<T>>` - Shared mutable state
- `thread::spawn` - Create threads
- `mpsc::channel` - Message passing
- `Atomic*` - Lock-free primitives

### System
- `std::fs` - File operations
- `std::path` - Path manipulation
- `std::env` - Environment variables
- `std::process` - Process spawning
- `std::net` - TCP/UDP networking
- `std::time` - Duration, Instant

### Core Traits
- `Clone` / `Copy` - Duplication
- `Debug` / `Display` - Formatting
- `From` / `Into` - Conversion
- `Iterator` - Iteration protocol
- `Send` / `Sync` - Thread safety
- `Error` - Error handling

## Version

- **Rust**: 1.92.0
- **Edition**: 2024
- **Last updated**: 2026-01-19
