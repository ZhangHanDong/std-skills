# I/O Traits Reference

## Read Trait

```rust
pub trait Read {
    fn read(&mut self, buf: &mut [u8]) -> io::Result<usize>;

    // Provided methods
    fn read_to_end(&mut self, buf: &mut Vec<u8>) -> io::Result<usize>;
    fn read_to_string(&mut self, buf: &mut String) -> io::Result<usize>;
    fn read_exact(&mut self, buf: &mut [u8]) -> io::Result<()>;
    fn bytes(self) -> Bytes<Self>;
    fn chain<R: Read>(self, next: R) -> Chain<Self, R>;
    fn take(self, limit: u64) -> Take<Self>;
}
```

### Implementors

- `File`
- `&[u8]` (slices)
- `Cursor<T>`
- `TcpStream`
- `Stdin`
- `BufReader<R>`

## Write Trait

```rust
pub trait Write {
    fn write(&mut self, buf: &[u8]) -> io::Result<usize>;
    fn flush(&mut self) -> io::Result<()>;

    // Provided methods
    fn write_all(&mut self, buf: &[u8]) -> io::Result<()>;
    fn write_fmt(&mut self, fmt: Arguments<'_>) -> io::Result<()>;
}
```

### Implementors

- `File`
- `Vec<u8>`
- `Cursor<&mut [u8]>`
- `TcpStream`
- `Stdout`, `Stderr`
- `BufWriter<W>`

## BufRead Trait

```rust
pub trait BufRead: Read {
    fn fill_buf(&mut self) -> io::Result<&[u8]>;
    fn consume(&mut self, amt: usize);

    // Provided methods
    fn read_until(&mut self, byte: u8, buf: &mut Vec<u8>) -> io::Result<usize>;
    fn read_line(&mut self, buf: &mut String) -> io::Result<usize>;
    fn lines(self) -> Lines<Self>;
}
```

### Implementors

- `BufReader<R>`
- `Cursor<T>`
- `&[u8]`
- `StdinLock`

## Seek Trait

```rust
pub trait Seek {
    fn seek(&mut self, pos: SeekFrom) -> io::Result<u64>;

    // Provided methods
    fn rewind(&mut self) -> io::Result<()>;
    fn stream_position(&mut self) -> io::Result<u64>;
}

pub enum SeekFrom {
    Start(u64),
    End(i64),
    Current(i64),
}
```

## Usage Examples

### Read

```rust
use std::io::Read;

let mut file = File::open("file.txt")?;
let mut buf = [0u8; 1024];
let n = file.read(&mut buf)?;

// Or read everything
let mut contents = Vec::new();
file.read_to_end(&mut contents)?;
```

### Write

```rust
use std::io::Write;

let mut file = File::create("file.txt")?;
file.write_all(b"hello")?;
file.flush()?;

// With formatting
writeln!(file, "value: {}", 42)?;
```

### BufRead

```rust
use std::io::{BufRead, BufReader};

let file = File::open("file.txt")?;
let reader = BufReader::new(file);

for line in reader.lines() {
    println!("{}", line?);
}
```

### Seek

```rust
use std::io::{Seek, SeekFrom};

let mut file = File::open("file.txt")?;
file.seek(SeekFrom::Start(100))?;  // Go to byte 100
file.seek(SeekFrom::End(-10))?;    // 10 bytes before end
file.rewind()?;                     // Back to start
```
