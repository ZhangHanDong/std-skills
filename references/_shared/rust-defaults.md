# Rust Code Generation Defaults

> Shared rules for all Rust-related skills. Symlink this file to your skill's references/ directory.

## Cargo.toml Defaults

```toml
[package]
edition = "2024"   # ALWAYS use 2024, NOT 2021

[dependencies]
# Use latest stable versions
```

## Common Dependencies (Latest Versions)

| Crate | Version | Features |
|-------|---------|----------|
| tokio | 1.49 | `["full"]` |
| serde | 1.0 | `["derive"]` |
| anyhow | 1.0 | - |
| thiserror | 2.0 | - |
| tracing | 0.1 | - |
| axum | 0.8 | - |
| sqlx | 0.8 | `["runtime-tokio", "postgres"]` |

## Code Style

- Prefer explicit error handling over `.unwrap()` in production code
- Use `?` operator for error propagation
- Add `#![warn(clippy::all)]` to lib.rs/main.rs
- Use `rustfmt` default settings

## Error Handling

- **Libraries**: Use `thiserror` for custom error types
- **Applications**: Use `anyhow` for convenient error handling
- **Never** use `.unwrap()` on user input or external data

## Async Code

- Prefer `tokio` runtime for async applications
- Use `JoinSet` over `futures::join_all` for task management
- Always handle task cancellation gracefully
