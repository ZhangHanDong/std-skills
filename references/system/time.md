# Time (std::time) Reference

## Duration

A span of time.

```rust
use std::time::Duration;

// Creation
let d = Duration::from_secs(5);
let d = Duration::from_millis(500);
let d = Duration::from_micros(500_000);
let d = Duration::from_nanos(1_000_000_000);
let d = Duration::new(5, 500_000_000);  // 5.5 seconds

// Constants
Duration::ZERO
Duration::MAX
Duration::SECOND  // 1.80+
```

### Duration Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `as_secs()` | `u64` | Whole seconds |
| `as_millis()` | `u128` | Total milliseconds |
| `as_micros()` | `u128` | Total microseconds |
| `as_nanos()` | `u128` | Total nanoseconds |
| `as_secs_f64()` | `f64` | Seconds as float |
| `subsec_millis()` | `u32` | Fractional milliseconds |
| `is_zero()` | `bool` | Check if zero |

### Duration Arithmetic

```rust
let a = Duration::from_secs(5);
let b = Duration::from_secs(3);

let sum = a + b;                    // 8 seconds
let diff = a - b;                   // 2 seconds
let scaled = a * 2;                 // 10 seconds
let divided = a / 2;                // 2.5 seconds
let saturating = a.saturating_sub(Duration::from_secs(10));  // 0
```

## Instant

Monotonic clock for measuring elapsed time.

```rust
use std::time::Instant;

let start = Instant::now();

// Do some work...

let elapsed = start.elapsed();
println!("Took: {:?}", elapsed);

// Compare instants
let later = Instant::now();
let duration = later - start;
```

### Instant Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `now()` | `Instant` | Current instant |
| `elapsed()` | `Duration` | Time since instant |
| `duration_since(earlier)` | `Duration` | Duration between instants |
| `checked_add(dur)` | `Option<Instant>` | Add duration |
| `saturating_duration_since(earlier)` | `Duration` | Safe subtraction |

## SystemTime

Wall clock time for timestamps.

```rust
use std::time::{SystemTime, UNIX_EPOCH};

// Current time
let now = SystemTime::now();

// Duration since UNIX epoch
let since_epoch = now.duration_since(UNIX_EPOCH)?;
println!("Unix timestamp: {}", since_epoch.as_secs());

// Time elapsed
let elapsed = now.elapsed()?;
```

### SystemTime Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `now()` | `SystemTime` | Current system time |
| `elapsed()` | `Result<Duration>` | Time since this point |
| `duration_since(earlier)` | `Result<Duration>` | Duration between times |
| `checked_add(dur)` | `Option<SystemTime>` | Add duration |

### UNIX_EPOCH

```rust
use std::time::{SystemTime, UNIX_EPOCH};

// Get Unix timestamp
let timestamp = SystemTime::now()
    .duration_since(UNIX_EPOCH)?
    .as_secs();

// Create time from timestamp
let time = UNIX_EPOCH + Duration::from_secs(1700000000);
```

## Instant vs SystemTime

| Feature | Instant | SystemTime |
|---------|---------|------------|
| Monotonic | Yes | No |
| Can go backwards | No | Yes (NTP adjustments) |
| Use case | Benchmarking, timeouts | Timestamps, file times |
| Thread-safe | Yes | Yes |

## Common Patterns

### Timing Code
```rust
let start = Instant::now();
expensive_function();
println!("Took: {:?}", start.elapsed());
```

### Timeout
```rust
let deadline = Instant::now() + Duration::from_secs(30);
while Instant::now() < deadline {
    // work...
}
```

### Rate Limiting
```rust
let interval = Duration::from_millis(100);
let mut last = Instant::now();

loop {
    let elapsed = last.elapsed();
    if elapsed < interval {
        std::thread::sleep(interval - elapsed);
    }
    last = Instant::now();
    // do work...
}
```
