# MPSC Channel (std::sync::mpsc) Reference

Multi-Producer, Single-Consumer FIFO queue for thread communication.

## Channel Types

| Type | Buffer | Blocking |
|------|--------|----------|
| `channel()` | Unbounded | Never on send |
| `sync_channel(n)` | Bounded (n) | Send blocks when full |
| `sync_channel(0)` | Rendezvous | Send blocks until recv |

## Basic Usage

### Asynchronous Channel
```rust
use std::sync::mpsc::channel;
use std::thread;

let (tx, rx) = channel();

thread::spawn(move || {
    tx.send("hello").unwrap();
});

let msg = rx.recv().unwrap();
```

### Synchronous Channel
```rust
use std::sync::mpsc::sync_channel;

let (tx, rx) = sync_channel(3);  // Buffer size 3

tx.send(1).unwrap();  // Won't block
tx.send(2).unwrap();
tx.send(3).unwrap();
// tx.send(4).unwrap();  // Would block!

rx.recv().unwrap();  // Now space available
```

## Multiple Producers

```rust
use std::sync::mpsc::channel;
use std::thread;

let (tx, rx) = channel();

for i in 0..5 {
    let tx = tx.clone();  // Clone sender
    thread::spawn(move || {
        tx.send(i).unwrap();
    });
}

drop(tx);  // Drop original sender

// Collect all messages
while let Ok(msg) = rx.recv() {
    println!("{}", msg);
}
```

## Sender Methods

| Method | Description |
|--------|-------------|
| `send(val)` | Send value, block if sync full |
| `clone()` | Create another sender |

### SyncSender Additional Methods
| Method | Description |
|--------|-------------|
| `try_send(val)` | Non-blocking send |
| `send_timeout(val, dur)` | Send with timeout |

## Receiver Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `recv()` | `Result<T, RecvError>` | Block until message |
| `try_recv()` | `Result<T, TryRecvError>` | Non-blocking |
| `recv_timeout(dur)` | `Result<T, RecvTimeoutError>` | With timeout |
| `iter()` | `Iter<T>` | Blocking iterator |
| `try_iter()` | `TryIter<T>` | Non-blocking iterator |

## Error Handling

```rust
use std::sync::mpsc::{channel, RecvError, TryRecvError};

let (tx, rx) = channel::<i32>();

// Disconnection
drop(tx);
assert!(matches!(rx.recv(), Err(RecvError)));

// Non-blocking empty
let (tx, rx) = channel::<i32>();
match rx.try_recv() {
    Err(TryRecvError::Empty) => println!("No message"),
    Err(TryRecvError::Disconnected) => println!("Closed"),
    Ok(v) => println!("{}", v),
}
```

## Patterns

### Request-Response
```rust
use std::sync::mpsc::channel;
use std::thread;

let (tx, rx) = channel();
let (resp_tx, resp_rx) = channel();

thread::spawn(move || {
    let request = rx.recv().unwrap();
    resp_tx.send(request * 2).unwrap();
});

tx.send(21).unwrap();
assert_eq!(resp_rx.recv().unwrap(), 42);
```

### Graceful Shutdown
```rust
use std::sync::mpsc::channel;
use std::thread;

let (tx, rx) = channel();

let worker = thread::spawn(move || {
    while let Ok(msg) = rx.recv() {
        println!("Processing: {}", msg);
    }
    println!("Worker shutting down");
});

tx.send("job1").unwrap();
tx.send("job2").unwrap();
drop(tx);  // Signal shutdown

worker.join().unwrap();
```

### Select-like with try_recv
```rust
use std::sync::mpsc::{channel, TryRecvError};
use std::thread;
use std::time::Duration;

let (tx1, rx1) = channel();
let (tx2, rx2) = channel();

loop {
    match rx1.try_recv() {
        Ok(msg) => println!("Channel 1: {}", msg),
        Err(TryRecvError::Empty) => {}
        Err(TryRecvError::Disconnected) => break,
    }

    match rx2.try_recv() {
        Ok(msg) => println!("Channel 2: {}", msg),
        Err(TryRecvError::Empty) => {}
        Err(TryRecvError::Disconnected) => break,
    }

    thread::sleep(Duration::from_millis(10));
}
```

## Comparison with crossbeam

| Feature | std::mpsc | crossbeam-channel |
|---------|-----------|-------------------|
| MPSC | Yes | Yes |
| MPMC | No | Yes |
| Select | No | Yes |
| Bounded | Yes | Yes |
| Unbounded | Yes | Yes |
| Performance | Good | Better |

For complex scenarios, consider `crossbeam-channel`.
