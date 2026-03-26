# Phase 4: Tauri Commands

## What You'll Build

Wire the database layer to the frontend via Tauri commands. Expose `get_todos`, `add_todo`, `mark_done`, and `delete_todo` as commands. Build a temporary, traditional UI (buttons + input) to test them. This UI gets removed in Phase 9.

## Learning Goals

- **A3**: Tauri Commands (Rust-to-JS Bridge)
- **A4**: Tauri Managed State

## Prerequisites

- Phase 3 complete: DB module with CRUD functions and passing tests

## Getting Started

Create a `commands.rs` module. Define Tauri commands that accept parameters from the frontend and call your DB functions. Register the DB connection as managed state so commands can access it.

On the Svelte side, use `@tauri-apps/api` to call these commands and display the results.

## Key Concepts

**Tauri commands**: Functions annotated with `#[tauri_command]` become callable from JavaScript. They can accept parameters (deserialized from JS) and return values (serialized back to JS). Serde handles the conversion automatically.

**Managed state**: Tauri lets you register shared state that any command can access via `State<T>`. This is how you share the database connection across commands without global variables. The pattern is: wrap your connection in a `Mutex` (because multiple commands might access it), register it with `manage()`, and accept `State<Mutex<Connection>>` as a command parameter.

**The IPC bridge**: When Svelte calls `invoke("get_todos")`, the request crosses from the webview into Rust, the command runs, and the result crosses back as JSON. Understanding this boundary is key to understanding Tauri.

## Done When

- Svelte UI can add a todo (text input + button), see all todos, mark one as done, and delete one
- All operations go through Tauri commands (no frontend-only state)
- DB connection is managed state, not a global
- You can explain what happens when `invoke()` is called — the full journey from JS to Rust and back

## Hints (Expand if Stuck)

<details>
<summary>Hint 1: State setup</summary>

In `main.rs`, during app setup:
```rust
// Pseudocode — implement the details yourself
let db = Connection::open(path)?;
// run schema creation
app.manage(Mutex::new(db));
```
</details>

<details>
<summary>Hint 2: Command signature</summary>

A Tauri command that uses managed state looks like:
```rust
#[tauri::command]
fn get_todos(db: State<'_, Mutex<Connection>>) -> Result<Vec<Todo>, String> {
    // lock the mutex, call your db function
}
```
Note the error type is `String` — Tauri commands need errors that implement `Serialize`.
</details>

<details>
<summary>Hint 3: Temporary UI</summary>

Don't spend time making the temporary UI pretty. A text input, an "Add" button, and a list with "Done" and "Delete" buttons next to each todo is enough. This is scaffolding that gets removed in Phase 9.
</details>
