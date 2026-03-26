# Architecture

## System Overview

```
┌─────────────────────────────────────────────────────────┐
│                     Tauri Window                         │
│                                                          │
│  ┌────────────────────────┐  ┌────────────────────────┐  │
│  │   Chat Panel (Svelte)  │  │ Todo Dashboard (Svelte)│  │
│  │                        │  │                        │  │
│  │  invoke("send_message")│  │  listen("todos-updated")│ │
│  │  invoke("get_history") │  │  invoke("get_todos")   │  │
│  └───────────┬────────────┘  └───────────▲────────────┘  │
│              │                           │               │
├──────────────┼───────────────────────────┼───────────────┤
│              │        Tauri IPC          │               │
│              ▼                           │               │
│  ┌─────────────────────────────────────────────────────┐ │
│  │              Rust Backend (src-tauri/)               │ │
│  │                                                      │ │
│  │  ┌──────────────┐   ┌──────────────┐   ┌─────────┐ │ │
│  │  │   Commands   │   │  LLM Client  │   │   DB    │ │ │
│  │  │              │   │              │   │         │ │ │
│  │  │ send_message─┼──▶│  Ollama /    │   │ SQLite  │ │ │
│  │  │ get_todos    │   │  OpenAI API  │   │         │ │ │
│  │  │ get_history  │   │              │   │ todos   │ │ │
│  │  └──────┬───────┘   └──────┬───────┘   │ messages│ │ │
│  │         │                  │            └────┬────┘ │ │
│  │         │                  ▼                 │      │ │
│  │         │         ┌──────────────┐           │      │ │
│  │         │         │ Intent Parser│           │      │ │
│  │         │         │              │           │      │ │
│  │         │         │ LlmIntent    │           │      │ │
│  │         │         │  enum match  ├───────────┘      │ │
│  │         │         └──────────────┘                  │ │
│  │         │                                           │ │
│  │         └──▶ emit("todos-updated") ─────────────────┘ │
│  └──────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────┘
```

## Data Flow: User sends a message

```
1. User types "add buy milk" in chat input
   │
2. Svelte calls invoke("send_message", { text: "add buy milk" })
   │
3. Tauri routes to Rust command handler
   │
4. Rust saves user message to SQLite (messages table)
   │
5. Rust builds system prompt:
   │  - Available operations (add, complete, delete, list, etc.)
   │  - Expected JSON output format
   │  - Current todo list as context
   │
6. Rust sends HTTP POST to LLM (Ollama localhost:11434)
   │
7. LLM returns: { "intent": "add", "title": "buy milk" }
   │
8. Rust parses JSON into LlmIntent::Add { title: "buy milk" }
   │
9. Rust match on LlmIntent → calls db::add_todo("buy milk")
   │
10. Rust constructs response: "Added 'buy milk' to your todos."
    │
11. Rust saves assistant message to SQLite (messages table)
    │
12. Rust emits "todos-updated" event via Tauri
    │
13. Rust returns ChatMessage to frontend
    │
14. Svelte appends message to chat panel
    │
15. Svelte receives "todos-updated" event → calls invoke("get_todos")
    │
16. Dashboard re-renders with new todo
```

## Module Structure (Rust)

```
src-tauri/src/
├── main.rs          ← Tauri app setup, command registration
├── commands.rs      ← Tauri command handlers (send_message, get_todos, etc.)
├── models.rs        ← Todo, TodoStatus, ChatMessage, LlmIntent structs/enums
├── db.rs            ← SQLite operations (CRUD for todos and messages)
└── llm/
    ├── mod.rs       ← LlmProvider trait, provider selection
    ├── ollama.rs    ← Ollama API client
    ├── openai.rs    ← OpenAI-compatible API client (Phase 10)
    └── prompt.rs    ← System prompt construction, response parsing
```

## Frontend Structure (Svelte)

```
src/
├── routes/
│   └── +page.svelte    ← Main layout: split panel
├── lib/
│   ├── components/
│   │   ├── ChatPanel.svelte      ← Message list + input
│   │   ├── ChatMessage.svelte    ← Single message bubble
│   │   ├── ChatInput.svelte      ← Text input + submit
│   │   ├── TodoDashboard.svelte  ← Read-only todo list
│   │   └── StatusBar.svelte      ← Provider + connection status
│   └── tauri.ts                  ← Typed wrappers for invoke/listen
```
