# Phase 3: SQLite Persistence

## What You'll Build

A `db` module with functions to initialize the schema, add/list/complete/delete todos, and save/load chat messages. All verified through integration tests against an in-memory SQLite instance.

## Learning Goals

- **A2**: SQLite Integration
- **F4**: Error Handling (Result, Option, ?)

## Prerequisites

- Phase 2 complete: data model structs/enums defined and tested

## Getting Started

Add `rusqlite` to your `Cargo.toml` dependencies (with the `bundled` feature so SQLite is compiled into the binary). Create a `db.rs` module. Start with schema creation, then implement CRUD functions one at a time, writing tests as you go.

## Key Concepts

**Result<T, E>**: Every database operation can fail. In Rust, failures are returned as `Result::Err(e)`, not thrown as exceptions. The `?` operator lets you propagate errors concisely — if the expression before `?` is an `Err`, the function returns early with that error.

**Connection management**: The SQLite connection is a resource you'll need to share across functions. For now, pass it as a parameter (`&Connection`). In Phase 4, you'll wrap it in Tauri's managed state.

**Parameterized queries**: Always use `?` placeholders in SQL, never string interpolation. This prevents SQL injection and is how `rusqlite` expects you to work.

## Done When

- `cargo test` passes with integration tests covering:
  - Schema creation on a fresh database
  - Adding a todo and retrieving it
  - Listing todos filtered by status
  - Marking a todo as complete
  - Deleting a todo
  - Saving and loading chat messages
- All functions return `Result<T, E>` and use `?` for error propagation
- You can explain why Rust uses `Result` instead of exceptions

## Hints (Expand if Stuck)

<details>
<summary>Hint 1: In-memory SQLite for tests</summary>

Use `Connection::open_in_memory()` for tests. Each test gets a fresh database — no cleanup needed. Call your schema creation function at the top of each test.
</details>

<details>
<summary>Hint 2: The bundled feature</summary>

In `Cargo.toml`:
```toml
[dependencies]
rusqlite = { version = "0.31", features = ["bundled"] }
```
The `bundled` feature compiles SQLite from source, so you don't need it installed separately.
</details>

<details>
<summary>Hint 3: Error types</summary>

Start simple: use `rusqlite::Error` directly as your error type. If you want something more flexible, add `anyhow` for ad-hoc errors or `thiserror` for custom error enums. Don't over-engineer this in Phase 3 — you'll revisit error handling in Phase 7.
</details>
