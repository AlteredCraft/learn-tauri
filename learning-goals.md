# Learning Goals

This document defines the Rust, Tauri, and Svelte learning objectives for the NatLang Todo build. Goals are organized into **foundations** (core Rust concepts) and **applied** (exercised through specific features). Each goal maps to one or more build phases so progress is tied to implementation, not separate study.

## How to use this document

- Goals are checked off when you can confidently explain the concept and have working code that demonstrates it
- The **Exercised in** field maps each goal to the feature/phase where you'll encounter it
- The **Notes** field is for capturing what clicked, what confused you, and where AI helped or hurt — raw material for reflection
- Claude Code's Learning mode references this document when deciding what `TODO(human)` items to assign

---

## Foundations

These are core Rust concepts that a web developer needs to internalize. They aren't features — they're the mental model shifts that make everything else make sense.

### F1: Ownership & Borrowing
- [ ] Can explain why Rust has ownership and what problem it solves
- [ ] Comfortable passing data to functions without the compiler fighting you
- [ ] Understand the difference between moving, borrowing (`&`), and mutable borrowing (`&mut`)
- **Exercised in**: Phase 2 — passing todo structs, chat messages, and database results between functions; Phase 7 — LLM client passing request/response data
- **Notes**:

### F2: Structs & Enums as Data Modeling
- [ ] Can define structs to represent domain objects (no classes, no inheritance)
- [ ] Can define enums with variants that carry data (algebraic data types)
- [ ] Understand `impl` blocks for attaching methods
- **Exercised in**: Phase 2 — `Todo` struct, `TodoStatus` enum, `LlmIntent` enum with associated data, `ChatMessage` struct
- **Notes**:

### F3: Pattern Matching
- [ ] Can use `match` on enums, Options, and Results
- [ ] Comfortable with destructuring in match arms
- [ ] Understand exhaustiveness checking and why the compiler insists on it
- **Exercised in**: Phase 2 — matching `TodoStatus`; Phase 8 — matching `LlmIntent` variants to dispatch the correct database operation
- **Notes**:

### F4: Error Handling (Result, Option, ?)
- [ ] Understand `Result<T, E>` vs exceptions — errors are values, not control flow
- [ ] Can use the `?` operator to propagate errors
- [ ] Can define custom error types or use a crate like `anyhow`/`thiserror`
- [ ] Know when to use `Option` vs `Result`
- **Exercised in**: Phase 3 — database operations that fail; Phase 7 — LLM API calls that fail, malformed responses; Phase 8 — ambiguous or unparseable LLM output
- **Notes**:

### F5: Traits
- [ ] Understand traits as Rust's interface mechanism
- [ ] Can implement standard traits (`Display`, `Debug`, `Serialize`, `Deserialize`)
- [ ] Understand derive macros and when to use them
- **Exercised in**: Phase 2 — serde derives on data structs, `Display` for `TodoStatus`; Phase 10 — `LlmProvider` trait for multi-provider support
- **Notes**:

### F6: Modules & Crates
- [ ] Can organize code into modules (`mod`, `pub`, `use`)
- [ ] Understand the file-system-to-module mapping convention
- [ ] Can add and use external crates via `Cargo.toml`
- [ ] Know how to evaluate a crate (docs.rs, crates.io, maintenance status)
- **Exercised in**: Phase 1 — project structure; ongoing — `db` module, `llm` module, `commands` module, `models` module
- **Notes**:

### F7: String Handling (String vs &str)
- [ ] Understand why Rust has two string types and when to use each
- [ ] Comfortable converting between them without fighting the compiler
- [ ] Can work with string slicing, formatting, and interpolation
- **Exercised in**: Everywhere — user input strings, LLM prompts, JSON serialization, database text fields, formatted chat responses
- **Notes**:

---

## Applied

These are Tauri, Svelte, and LLM integration concepts exercised through specific features. Each maps directly to a build phase.

### A1: Tauri App Scaffolding & Lifecycle
- [ ] Can navigate and explain every file in a Tauri 2 project
- [ ] Understand the Tauri app lifecycle (setup, run, exit)
- [ ] Understand the relationship between the Rust backend and the webview frontend
- [ ] Can configure app metadata, window properties, and permissions
- [ ] Can trace an IPC call from frontend `invoke()` through to a Rust command and back
- **Exercised in**: Phase 1 — explore the pre-built scaffold, verify dev workflow, understand the IPC bridge
- **Notes**:

### A2: SQLite Integration
- [ ] Can set up an embedded SQLite database from Rust
- [ ] Can define a schema and run migrations
- [ ] Can perform CRUD operations with parameterized queries
- [ ] Understand connection management patterns
- **Exercised in**: Phase 3 — todo storage, chat history persistence, schema creation on first launch
- **Notes**:

### A3: Tauri Commands (Rust-to-JS Bridge)
- [ ] Can define Tauri commands that expose Rust functions to the frontend
- [ ] Can pass structured data (structs/enums) between Rust and JS via serde
- [ ] Understand command return types and error handling across the bridge
- **Exercised in**: Phase 4 — `send_message`, `get_todos`, `get_chat_history` commands
- **Notes**:

### A4: Tauri Managed State
- [ ] Can register and access shared application state in Tauri
- [ ] Understand how Tauri's state management interacts with Rust's ownership model
- [ ] Can safely share state between commands
- **Exercised in**: Phase 4 — DB connection and LLM client config shared across all commands
- **Notes**:

### A5: Tauri Events (Backend-to-Frontend Push)
- [ ] Can emit events from Rust to the frontend
- [ ] Can listen for events in the JS frontend and update the UI
- [ ] Understand the event model vs. command model (push vs. pull)
- **Exercised in**: Phase 6 — `todos-updated` event emitted after any todo mutation, dashboard refreshes reactively
- **Notes**:

### A6: HTTP Client & Async
- [ ] Understand Rust's async model (futures, runtimes, tokio)
- [ ] Can make HTTP requests and handle responses
- [ ] Can deserialize API responses into Rust structs
- [ ] Understand async error handling
- **Exercised in**: Phase 7 — Ollama REST API calls; Phase 10 — OpenAI-compatible API calls
- **Notes**:

### A7: Serde & JSON
- [ ] Can serialize/deserialize Rust structs to/from JSON
- [ ] Understand serde's derive model and field attributes (`#[serde(rename)]`, `#[serde(default)]`)
- [ ] Can handle structured LLM responses
- **Exercised in**: Phase 7 — LLM request/response serialization; Phase 4 — Tauri command data transfer
- **Notes**:

### A8: Svelte 5 Reactivity & Runes
- [ ] Understand Svelte 5 runes (`$state`, `$derived`, `$effect`)
- [ ] Can build reactive components that update from external data
- [ ] Can handle user input and form submission
- **Exercised in**: Phase 5 — chat message list, todo dashboard, message input; Phase 9 — chat history loading, loading states
- **Notes**:

### A9: Svelte Component Architecture
- [ ] Can decompose UI into reusable components
- [ ] Understand props, events, and slots in Svelte 5
- [ ] Can style components with scoped CSS
- **Exercised in**: Phase 5 — ChatMessage, TodoList, ChatInput, Layout components; Phase 9 — polish and refinement
- **Notes**:

### A10: Tauri Frontend Integration
- [ ] Can call Tauri commands from Svelte via `@tauri-apps/api`
- [ ] Can listen for Tauri events and react to them
- [ ] Understand the security model (CSP, allowed domains)
- **Exercised in**: Phase 5 — invoking `send_message`; Phase 6 — listening for `todos-updated`; Phase 5 — calling `get_chat_history` on mount
- **Notes**:

### A11: LLM Prompt Engineering in Code
- [ ] Can construct effective system prompts programmatically
- [ ] Understand structured output formats (JSON mode, schema guidance)
- [ ] Can handle LLM response parsing failures gracefully
- [ ] Can iterate on prompts based on observed behavior
- **Exercised in**: Phase 8 — building the system prompt with todo context, parsing structured responses, fallback handling
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
