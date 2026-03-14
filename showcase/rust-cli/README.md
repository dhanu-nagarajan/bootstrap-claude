# Showcase: Rust CLI Tool

This showcase demonstrates what bootstrap-claude generates for a Rust CLI application using the lite tier.

**Hypothetical project:** A file synchronization CLI that watches directories and syncs changes to cloud storage.

**Stack:** Rust, clap, tokio, serde, reqwest

**Tier:** lite (4 files)

## Generated Files

```
.claude/
├── standards/
│   └── forbidden.md       ← 11 Rust anti-patterns
└── shield/
    ├── invariants.md      ← Top 8 rules
    ├── session-state.md   ← Session tracking
    └── recovery-protocol.md ← Recovery procedure
```

## Why Lite Tier Works Here

This is a focused CLI tool with clear conventions enforced by Rust's compiler and clippy. The lite tier provides:
1. **Forbidden patterns** that clippy doesn't catch (architectural anti-patterns, domain-specific rules)
2. **Compaction protection** for long debugging/feature sessions

The developer can upgrade to `--standard` when they want architecture docs and checklists.

## Highlights

### forbidden.md — Beyond What Clippy Catches

- "`unwrap()` in any code path reachable from main — use `?` or `expect()` with context message"
- "`clone()` to satisfy the borrow checker — restructure ownership or use `Rc`/`Arc`"
- "`unsafe` blocks without a `// SAFETY:` comment explaining the invariant"
- "Synchronous I/O in async functions — use `tokio::fs` not `std::fs`"
- "`Box<dyn Error>` in library code — use `thiserror` for typed errors"
- "`println!`/`eprintln!` — use `tracing` for structured logging"

### shield/invariants.md — Minimal but Effective

Only 8 rules, under 800 tokens. Includes:
- Build: `cargo test`, `cargo clippy -- -D warnings`
- Architecture: all cloud API calls go through `src/client/` module
- Error handling: `thiserror` for library errors, `anyhow` for binary
- Async: all I/O uses tokio, no blocking in async context
