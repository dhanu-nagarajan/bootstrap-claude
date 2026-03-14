# Reference Example: Rust Project Output

This shows what high-quality, project-specific output looks like for a Rust project. Use as a calibration reference — your output should be THIS specific, never more generic.

## Example: standards/language.md (for a Rust CLI/library project)

```markdown
# Rust Conventions

## Naming
- **Crates/modules**: snake_case (`user_service`, `auth_middleware`)
- **Types/Traits**: PascalCase (`UserProfile`, `Serializable`)
- **Functions/methods**: snake_case (`get_user_by_id`, `parse_config`)
- **Constants**: SCREAMING_SNAKE (`MAX_RETRIES`, `DEFAULT_TIMEOUT`)
- **Type parameters**: single capital letter for simple, descriptive for complex (`T`, `Item`, `K, V`)
- **Lifetimes**: short lowercase (`'a`, `'de`); descriptive when multiple (`'input`, `'output`)
- **Feature flags**: kebab-case (`serde-support`, `async-runtime`)
- **Test modules**: `#[cfg(test)] mod tests` at bottom of file

## Type Discipline
- `clippy::pedantic` enabled — all warnings addressed or explicitly allowed with reason
- Prefer `&str` over `String` in function parameters (borrow, don't own)
- Use `thiserror` for library error types, `anyhow` for application error types
- Newtype pattern for domain types (`struct UserId(Uuid)` not raw `Uuid`)
- `#[must_use]` on all functions returning `Result` or computed values
- `#[non_exhaustive]` on public enums to allow future variants

## Patterns (from codebase analysis)

### Error Handling
```rust
// Pattern found in src/error.rs — thiserror for structured errors
#[derive(Debug, thiserror::Error)]
pub enum AppError {
    #[error("User not found: {id}")]
    UserNotFound { id: UserId },

    #[error("Invalid input: {message}")]
    Validation { message: String, field: &'static str },

    #[error("Database error")]
    Database(#[from] sqlx::Error),

    #[error("Authentication failed")]
    Unauthorized,
}

impl AppError {
    pub fn status_code(&self) -> StatusCode {
        match self {
            Self::UserNotFound { .. } => StatusCode::NOT_FOUND,
            Self::Validation { .. } => StatusCode::BAD_REQUEST,
            Self::Database(_) => StatusCode::INTERNAL_SERVER_ERROR,
            Self::Unauthorized => StatusCode::UNAUTHORIZED,
        }
    }
}
```

### Builder Pattern
```rust
// Pattern found in src/config.rs — builder for complex construction
pub struct ServerConfig {
    port: u16,
    host: String,
    max_connections: usize,
}

impl ServerConfig {
    pub fn builder() -> ServerConfigBuilder {
        ServerConfigBuilder::default()
    }
}

#[derive(Default)]
pub struct ServerConfigBuilder {
    port: Option<u16>,
    host: Option<String>,
    max_connections: Option<usize>,
}

impl ServerConfigBuilder {
    pub fn port(mut self, port: u16) -> Self {
        self.port = Some(port);
        self
    }

    pub fn build(self) -> Result<ServerConfig, ConfigError> {
        Ok(ServerConfig {
            port: self.port.unwrap_or(8080),
            host: self.host.unwrap_or_else(|| "127.0.0.1".into()),
            max_connections: self.max_connections.unwrap_or(100),
        })
    }
}
```

### Module Organization
```rust
// src/lib.rs — public API surface
pub mod config;
pub mod error;
pub mod models;
pub mod handlers;

// Re-export primary types for convenience
pub use config::AppConfig;
pub use error::{AppError, Result};
```
```

## Example: standards/forbidden.md (for a Rust project)

```markdown
# Forbidden Patterns

These trigger immediate rejection in code review. No exceptions without ADR.

- `unwrap()` in production code — Use `?` operator, `unwrap_or`, `unwrap_or_else`, or `expect` with message
- `expect()` without descriptive message — Always explain WHY this should never fail
- `unsafe` without `// SAFETY:` comment — Every unsafe block must document the invariants being upheld
- `clone()` to satisfy the borrow checker — Fix the ownership; cloning is usually a design smell
- `String` in struct fields that should be `&str` or `Cow<str>` — Unnecessary allocation
- `Box<dyn Error>` in library code — Use `thiserror` for typed errors; `anyhow` is for binaries only
- `println!` or `eprintln!` in library code — Use `tracing` or `log` crate
- `std::thread::sleep` in async code — Blocks the runtime; use `tokio::time::sleep`
- Nested `match` more than 2 levels — Refactor with helper methods or `and_then` chains
- `pub` on struct fields without justification — Default to private; expose via methods
- `#[allow(unused)]` on production code — Remove dead code; don't silence the compiler
- Manual `impl Display` when `thiserror` would suffice — Reduce boilerplate
- `Vec<Box<dyn Trait>>` when `enum` dispatch suffices — Enum dispatch is faster and type-safe
- `.to_string()` in hot paths — Use `Cow<str>` or write directly to buffers
```

## Example: checklists/pre-commit.md (for a Rust project)

```markdown
## Pre-Commit Checklist

### Automated (must all pass)
- [ ] `cargo check` — Compilation check
- [ ] `cargo clippy -- -D warnings` — Lint with pedantic rules
- [ ] `cargo fmt --check` — Formatting check
- [ ] `cargo test` — Full test suite
- [ ] `cargo doc --no-deps` — Documentation builds without warnings

### Manual Verification
- [ ] No `todo!()`, `unimplemented!()`, or `dbg!()` in diff
- [ ] No hardcoded secrets, API keys, or file paths in diff
- [ ] No unrelated changes bundled in this commit
- [ ] New public API has doc comments with examples
- [ ] New `unsafe` blocks have `// SAFETY:` comments
- [ ] Error types cover all failure modes (no catch-all variants)
- [ ] Commit message: imperative mood, explains WHY
```
