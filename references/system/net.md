# Network (std::net) Reference

## TCP

### Server (TcpListener)
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

### Client (TcpStream)
```rust
use std::net::TcpStream;
use std::io::{Read, Write};

let mut stream = TcpStream::connect("127.0.0.1:8080")?;
stream.write_all(b"Hello")?;

let mut response = String::new();
stream.read_to_string(&mut response)?;
```

## UDP

```rust
use std::net::UdpSocket;

// Bind to local address
let socket = UdpSocket::bind("127.0.0.1:34254")?;

// Send to specific address
socket.send_to(b"Hello", "127.0.0.1:8080")?;

// Receive
let mut buf = [0; 1024];
let (len, src) = socket.recv_from(&mut buf)?;

// Connect (optional, for specific peer)
socket.connect("127.0.0.1:8080")?;
socket.send(b"Hello")?;
socket.recv(&mut buf)?;
```

## IP Addresses

```rust
use std::net::{IpAddr, Ipv4Addr, Ipv6Addr};

// IPv4
let v4 = Ipv4Addr::new(127, 0, 0, 1);
let v4: Ipv4Addr = "127.0.0.1".parse()?;

// IPv6
let v6 = Ipv6Addr::new(0, 0, 0, 0, 0, 0, 0, 1);
let v6: Ipv6Addr = "::1".parse()?;

// Generic IP
let ip: IpAddr = "127.0.0.1".parse()?;
let ip = IpAddr::V4(Ipv4Addr::LOCALHOST);
```

## Socket Addresses

```rust
use std::net::{SocketAddr, SocketAddrV4, SocketAddrV6};

// Generic socket address
let addr: SocketAddr = "127.0.0.1:8080".parse()?;

// IPv4 socket address
let addr = SocketAddrV4::new(Ipv4Addr::new(127, 0, 0, 1), 8080);

// IPv6 socket address
let addr = SocketAddrV6::new(Ipv6Addr::LOCALHOST, 8080, 0, 0);
```

## Common Methods

### TcpListener
| Method | Description |
|--------|-------------|
| `bind(addr)` | Create listener on address |
| `accept()` | Accept single connection |
| `incoming()` | Iterator of connections |
| `local_addr()` | Get bound address |
| `set_nonblocking(bool)` | Set non-blocking mode |

### TcpStream
| Method | Description |
|--------|-------------|
| `connect(addr)` | Connect to server |
| `peer_addr()` | Remote address |
| `local_addr()` | Local address |
| `shutdown(how)` | Shutdown read/write/both |
| `set_read_timeout(dur)` | Set read timeout |
| `set_nodelay(bool)` | Disable Nagle algorithm |

### UdpSocket
| Method | Description |
|--------|-------------|
| `bind(addr)` | Bind to local address |
| `connect(addr)` | Set default peer |
| `send(buf)` | Send to connected peer |
| `send_to(buf, addr)` | Send to specific address |
| `recv(buf)` | Receive from connected peer |
| `recv_from(buf)` | Receive with sender address |

## ToSocketAddrs Trait

Enables flexible address specification:

```rust
use std::net::TcpStream;

// All these work
TcpStream::connect("127.0.0.1:8080")?;
TcpStream::connect(("127.0.0.1", 8080))?;
TcpStream::connect(("localhost", 8080))?;
```
