# Phase 6: Tauri Events for Reactivity

## What You'll Build

When a todo is created, completed, or deleted via a Tauri command, the Rust backend emits a `todos-updated` event. The Svelte frontend listens for this event and refreshes the dashboard — no polling, no manual refresh.

## Learning Goals

- **A5**: Tauri Events (Backend-to-Frontend Push)

## Prerequisites

- Phase 5 complete: split-panel UI rendering, chat working in echo mode

## Getting Started

This is a focused phase — one concept, one implementation. In your Rust command handlers, after any database mutation, emit an event using the Tauri app handle. In Svelte, listen for the event and re-fetch the todo list.

## Key Concepts

**Push vs. pull**: Commands are pull-based — the frontend asks Rust for data. Events are push-based — Rust tells the frontend something happened. Events are the right pattern when the backend changes state and the frontend needs to know.

**Why events matter here**: Without events, the todo dashboard would need to poll `get_todos` on a timer or re-fetch after every chat message. Events are cleaner — the dashboard updates exactly when data changes, instantly.

**The app handle**: To emit events from a command, you need access to the Tauri `AppHandle`. Pass it as a command parameter (Tauri provides it automatically).

## Done When

- After adding/completing/deleting a todo, the dashboard updates immediately
- No polling or manual refresh logic in the frontend
- You can explain the difference between Tauri commands and Tauri events
- You can explain when you'd use events vs. command return values

## Hints (Expand if Stuck)

<details>
<summary>Hint 1: Emitting from Rust</summary>

In your command handler:
```rust
// After mutating the database:
app_handle.emit("todos-updated", ()).unwrap();
```
The second argument is the event payload — `()` means no payload (the frontend just needs to know something changed).
</details>

<details>
<summary>Hint 2: Listening in Svelte</summary>

```typescript
import { listen } from '@tauri-apps/api/event';

// In your component:
$effect(() => {
    const unlisten = listen('todos-updated', () => {
        // re-fetch todos
    });
    return () => { unlisten.then(fn => fn()); };
});
```
</details>

<details>
<summary>Hint 3: Getting the AppHandle</summary>

Add `app_handle: tauri::AppHandle` as a parameter to any command that needs to emit events. Tauri injects it automatically — you don't need to pass it from JavaScript.
</details>
