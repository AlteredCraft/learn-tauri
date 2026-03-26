# Learning Goals

This document defines the Rust, Tauri, and Svelte learning objectives for the NatLang Todo build. Goals are organized into **foundations** (core Rust concepts) and **applied** (exercised through specific features). Each goal maps to one or more build phases so progress is tied to implementation, not separate study.

## How to use this document

- Each goal has **evidence** items — concrete, observable things the learner did or explained. Check these off as they happen.
- The **Exercised in** field maps each goal to the feature/phase where you'll encounter it
- The **Notes** field captures what clicked, what was confusing, and where AI helped or hurt
- Claude Code's Learning mode references this document when deciding what `TODO(human)` items to assign and what questions to ask
- The agent updates this file during `/checkpoint` — the learner doesn't need to edit it directly

---

## Foundations

These are core Rust concepts that a web developer needs to internalize. They aren't features — they're the mental model shifts that make everything else make sense.

### F1: Ownership & Borrowing
- [ ] Wrote a function that takes ownership of a value and used it without compiler errors
- [ ] Fixed a borrow checker error by changing `self` to `&self` or `&mut self` and explained why
- [ ] Explained in own words what problem ownership solves (not just "the compiler said so")
- [ ] Correctly predicted whether a value would need to be borrowed or moved before the compiler told them
- **Exercised in**: Phase 2 — passing todo structs between functions; Phase 7 — LLM client passing request/response data
- **Notes**:

### F2: Structs & Enums as Data Modeling
- [ ] Defined a struct with appropriate field types (choosing between String, Option, Vec, etc.)
- [ ] Defined an enum where at least one variant carries data
- [ ] Wrote an `impl` block with at least one method
- [ ] Explained why Rust uses structs + enums instead of classes + inheritance
- **Exercised in**: Phase 2 — `Todo` struct, `TodoStatus` enum, `LlmIntent` enum with associated data, `ChatMessage` struct
- **Notes**:

### F3: Pattern Matching
- [ ] Wrote a `match` that destructures an enum variant to extract its data
- [ ] Encountered an exhaustiveness error, added the missing arm, and explained why Rust requires it
- [ ] Used pattern matching on `Option` or `Result` (not just `.unwrap()`)
- **Exercised in**: Phase 2 — matching `TodoStatus`; Phase 8 — matching `LlmIntent` variants to dispatch the correct database operation
- **Notes**:

### F4: Error Handling (Result, Option, ?)
- [ ] Wrote a function that returns `Result<T, E>` and used `?` to propagate errors
- [ ] Handled an error case explicitly (not with `.unwrap()` or `.expect()` in production code)
- [ ] Chose between `Option` and `Result` for a return type and explained the choice
- [ ] Explained how `Result` differs from try/catch in JS/Python
- **Exercised in**: Phase 3 — database operations that fail; Phase 7 — LLM API calls that fail; Phase 8 — unparseable LLM output
- **Notes**:

### F5: Traits
- [ ] Added `#[derive(...)]` to a struct and explained what traits were being implemented
- [ ] Implemented a trait manually (e.g., `Display` for a type)
- [ ] Defined a custom trait with at least one method (Phase 10)
- [ ] Explained what traits give you that JS/TS interfaces don't
- **Exercised in**: Phase 2 — serde derives, `Display` for `TodoStatus`; Phase 10 — `LlmProvider` trait
- **Notes**:

### F6: Modules & Crates
- [ ] Created a new module file and wired it into the project with `mod` and `pub`
- [ ] Added an external crate to `Cargo.toml` and used it in code
- [ ] Explained the file-system-to-module mapping (why `mod db;` expects `db.rs` or `db/mod.rs`)
- **Exercised in**: Phase 1 — project structure; ongoing — `db`, `llm`, `commands`, `models` modules
- **Notes**:

### F7: String Handling (String vs &str)
- [ ] Fixed a type mismatch between `String` and `&str` and explained why both exist
- [ ] Chose the right string type for a function parameter and explained the choice
- [ ] Used `format!()` or string interpolation to build a dynamic string
- **Exercised in**: Everywhere — user input, LLM prompts, JSON serialization, database text, chat responses
- **Notes**:

---

## Applied

These are Tauri, Svelte, and LLM integration concepts exercised through specific features. Each maps directly to a build phase.

### A1: Tauri App Scaffolding & Lifecycle
- [ ] Traced the `invoke("greet")` call from Svelte through IPC to Rust and back, explaining each step
- [ ] Explained why `main.rs` and `lib.rs` are separate files
- [ ] Modified `tauri.conf.json` (e.g., window size, title) and observed the effect
- [ ] Explained why SvelteKit sets `ssr = false` in a Tauri app
- **Exercised in**: Phase 1 — explore the pre-built scaffold, verify dev workflow, understand the IPC bridge
- **Notes**:

### A2: SQLite Integration
- [ ] Wrote a `CREATE TABLE` statement and executed it from Rust
- [ ] Wrote a parameterized INSERT and SELECT query (not string interpolation)
- [ ] Wrote a test that uses an in-memory database
- [ ] Explained why parameterized queries matter (vs. string formatting)
- **Exercised in**: Phase 3 — todo storage, chat history persistence, schema creation on first launch
- **Notes**:

### A3: Tauri Commands (Rust-to-JS Bridge)
- [ ] Defined a `#[tauri::command]` function that accepts parameters and returns structured data
- [ ] Called the command from Svelte with `invoke()` and handled the response
- [ ] Hit a serialization error across the IPC bridge and fixed it
- **Exercised in**: Phase 4 — `send_message`, `get_todos`, `get_chat_history` commands
- **Notes**:

### A4: Tauri Managed State
- [ ] Registered shared state with `app.manage()` and accessed it in a command via `State<T>`
- [ ] Wrapped state in `Mutex` and explained why concurrent access needs it
- [ ] Explained the difference between managed state and a global variable
- **Exercised in**: Phase 4 — DB connection and LLM client config shared across all commands
- **Notes**:

### A5: Tauri Events (Backend-to-Frontend Push)
- [ ] Emitted an event from Rust with `app_handle.emit()`
- [ ] Listened for the event in Svelte and triggered a UI update
- [ ] Explained when to use events vs. command return values
- **Exercised in**: Phase 6 — `todos-updated` event emitted after any todo mutation
- **Notes**:

### A6: HTTP Client & Async
- [ ] Made an async HTTP request with `reqwest` and awaited the response
- [ ] Deserialized a JSON response into a Rust struct
- [ ] Handled a network error (timeout, connection refused) without panicking
- [ ] Explained what `.await` does and how Rust async differs from JS async
- **Exercised in**: Phase 7 — Ollama REST API calls; Phase 10 — OpenAI-compatible API calls
- **Notes**:

### A7: Serde & JSON
- [ ] Serialized a Rust struct to JSON and deserialized JSON back to a struct
- [ ] Used a serde attribute (`#[serde(rename)]`, `#[serde(default)]`, etc.) and explained why
- [ ] Debugged a serde deserialization failure (wrong field name, missing field, wrong type)
- **Exercised in**: Phase 7 — LLM request/response serialization; Phase 4 — Tauri command data transfer
- **Notes**:

### A8: Svelte 5 Reactivity & Runes
- [ ] Used `$state` to create a reactive variable and saw the UI update when it changed
- [ ] Used `$derived` to compute a value from other state
- [ ] Used `$effect` for a side effect (e.g., auto-scroll, fetch on mount)
- [ ] Explained how Svelte 5 runes compare to React hooks or Vue reactivity
- **Exercised in**: Phase 5 — chat message list, todo dashboard, message input; Phase 9 — chat history, loading states
- **Notes**:

### A9: Svelte Component Architecture
- [ ] Created at least 3 separate `.svelte` component files
- [ ] Passed data from a parent to a child component via props
- [ ] Styled a component with scoped CSS
- **Exercised in**: Phase 5 — ChatMessage, TodoList, ChatInput, Layout components; Phase 9 — polish
- **Notes**:

### A10: Tauri Frontend Integration
- [ ] Called a Tauri command from Svelte and displayed the result
- [ ] Set up an event listener with `listen()` that updates component state
- [ ] Explained the difference between `invoke()` (pull) and `listen()` (push)
- **Exercised in**: Phase 5 — invoking commands; Phase 6 — listening for events
- **Notes**:

### A11: LLM Prompt Engineering in Code
- [ ] Built a system prompt dynamically from application state (not a static string)
- [ ] Parsed structured JSON from an LLM response into a Rust type
- [ ] Handled a malformed LLM response without crashing (fallback logic)
- [ ] Iterated on a prompt after observing incorrect classification and improved it
- **Exercised in**: Phase 8 — system prompt with todo context, structured output parsing, fallback handling
- **Notes**:

---

## Suggested Build Order

This sequence front-loads foundational Rust learning through data modeling and persistence, before adding Tauri IPC, the chat UI, and LLM integration.

| Phase | What to build | Primary learning goals |
|---|---|---|
| 1 | Explore scaffold, run dev workflow, understand Tauri architecture | A1, F6 |
| 2 | Data model in Rust — structs, enums, unit tests (no UI) | F1, F2, F3, F5, F7 |
| 3 | SQLite persistence — schema, CRUD functions, integration tests | A2, F4 |
| 4 | Tauri commands — wire DB to frontend, temporary CRUD controls | A3, A4 |
| 5 | Chat interface — split-panel Svelte UI, echo mode (no LLM) | A8, A9, A10 |
| 6 | Tauri events — backend pushes updates, dashboard reacts | A5 |
| 7 | LLM client — Rust HTTP to Ollama, request/response structs | A6, A7, F4 |
| 8 | NL pipeline — system prompt, intent classification, dispatch | A11, F3 |
| 9 | Remove temp controls, chat history persistence, polish | A8, A9 |
| 10 | Multi-provider support — LlmProvider trait, OpenAI-compatible | A6, F5 |
