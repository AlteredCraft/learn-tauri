---
title: NatLang Todo
sub-title: Product specification for a natural-language-only todo application
tags: [learn-by-building, tauri, rust, svelte, llm]
premise: A publishable learning resource — fork the repo, open Claude Code, build the app through 10 progressive phases
---

# NatLang Todo

## 1. Project Overview

A desktop todo application whose only interface is natural language. There are no buttons, checkboxes, or forms for managing todos — you type what you want in a chat interface, an LLM interprets your intent, Rust executes the operation against SQLite, and the UI updates reactively.

### Core Problem

Traditional todo apps put all interesting logic in the frontend (form handling, state management, click handlers). The backend becomes a dumb CRUD API. This design inverts that: the Rust backend does all the heavy lifting (LLM orchestration, intent classification, database operations, event emission), making it an ideal vehicle for learning Tauri 2 and Rust.

### What It Is

- A split-panel desktop app: chat interface (left) + read-only todo dashboard (right)
- Natural language as the only control surface — "add buy milk", "mark groceries as done", "show everything tagged work"
- LLM-powered intent classification with structured output (the LLM classifies, Rust executes)
- SQLite persistence for todos and chat history
- Reactive dashboard that updates via Tauri events

### What It Is Not

- Not a chatbot — the LLM does classification and extraction, not conversation
- Not dependent on a specific LLM provider — configurable, Ollama by default
- Not a production app — it's a learning vehicle with a useful outcome

### Tech Stack

- **Backend**: Rust — [book](https://doc.rust-lang.org/stable/book/) | [std library](https://doc.rust-lang.org/stable/std/) | [language reference](https://doc.rust-lang.org/stable/reference/index.html)
- **Desktop framework**: Tauri 2 — [docs](https://v2.tauri.app/) | [llms.txt](https://v2.tauri.app/llms.txt)
- **Frontend**: Svelte 5 + SvelteKit — [Svelte docs](https://svelte.dev/docs/svelte/overview) | [SvelteKit docs](https://svelte.dev/docs/kit/introduction) | [llms.txt](https://svelte.dev/llms.txt)
- **Database**: SQLite (embedded, via `rusqlite`)
- **LLM**: Ollama (local, default) or OpenAI-compatible API
- **HTTP client**: `reqwest` for LLM API calls
- **Package manager**: pnpm

### Secondary Purpose

This project is the Tauri case study for an article exploring AI-assisted learning, using Claude Code's Learning output style with structured anti-brain-rot guardrails.

---

## 2. Data Model

### Todo

| Field | Type | Description |
|---|---|---|
| id | INTEGER | Auto-incrementing primary key |
| title | TEXT | The todo description |
| status | TEXT | `pending` or `done` |
| priority | TEXT | Optional: `low`, `medium`, `high` |
| tags | TEXT | Comma-separated tags, optional |
| created_at | TEXT | ISO 8601 timestamp |
| completed_at | TEXT | ISO 8601, null if pending |
| updated_at | TEXT | ISO 8601, updated on any change |

### ChatMessage

| Field | Type | Description |
|---|---|---|
| id | INTEGER | Auto-incrementing primary key |
| role | TEXT | `user` or `assistant` |
| content | TEXT | The message text |
| timestamp | TEXT | ISO 8601 |

### TodoStatus (Rust enum)

```
Pending → Done
```

### LlmIntent (Rust enum)

The LLM's structured response is parsed into one of these variants:

| Intent | Associated Data | Example Input |
|---|---|---|
| Add | title, optional priority, optional tags | "add buy milk" |
| Complete | identifier (title substring or id) | "mark buy milk as done" |
| Delete | identifier | "delete the dentist reminder" |
| List | optional filter (status, tag, priority) | "show all pending todos" |
| Update | identifier, fields to change | "change priority of milk to high" |
| Tag | identifier, tags to add | "tag groceries with shopping" |
| Search | query string | "find anything about work" |
| Unclear | raw text | (fallback when LLM can't classify) |

---

## 3. LLM Integration

### Provider Architecture

The app supports multiple LLM backends via configuration. The Rust backend abstracts the provider behind a common interface.

**Default: Ollama (local)**
- Endpoint: `http://localhost:11434/api/chat`
- Recommended model: `llama3.2:3b` or `qwen2.5:3b`
- No API key required, works offline

**Alternative: OpenAI-compatible API**
- Any provider that implements the `/v1/chat/completions` endpoint
- Covers: OpenAI, Groq, Together, local vLLM, etc.
- Configured via `.env`: `LLM_BASE_URL` + `LLM_API_KEY`

### Interaction Pattern

The LLM's role is narrow: **classification + extraction**, not free-form generation.

1. Rust constructs a system prompt containing:
   - Available operations (the intent enum)
   - Expected JSON output format with schema
   - Current todo list as context (so the LLM can resolve references like "the milk one")
2. User's message is sent as the user turn
3. LLM returns structured JSON: `{ "intent": "add", "title": "buy milk", "priority": "medium" }`
4. Rust parses the JSON into an `LlmIntent` enum variant
5. Rust dispatches the intent to the correct database operation
6. Rust constructs a human-friendly response message
7. Response is returned to the frontend

### Error Handling

- **LLM unreachable**: Return a chat message explaining the connection issue, suggest checking Ollama
- **Malformed JSON**: Attempt lenient parsing; if that fails, return "I didn't understand that — try rephrasing"
- **Ambiguous intent**: The LLM should return an `Unclear` intent with a clarification question
- **No matching todo**: Return a helpful message listing similar todos

---

## 4. User Interface

### Layout

Split panel, no window chrome beyond Tauri defaults:

```
┌──────────────────────────────────────────────────────┐
│                    NatLang Todo                        │
├────────────────────────────┬─────────────────────────┤
│                            │                         │
│       Chat Panel           │    Todo Dashboard       │
│       (60% width)          │    (40% width)          │
│                            │                         │
│  ┌──────────────────────┐  │  ┌───────────────────┐  │
│  │ assistant: Added     │  │  │ Pending (3)       │  │
│  │ "buy milk" to your   │  │  │ ☐ buy milk        │  │
│  │ todos.               │  │  │ ☐ call dentist    │  │
│  │                      │  │  │ ☐ finish report   │  │
│  │ user: mark milk done │  │  │                   │  │
│  │                      │  │  │ Done (2)          │  │
│  │ assistant: Done!     │  │  │ ☑ groceries       │  │
│  │ "buy milk" is now    │  │  │ ☑ email boss      │  │
│  │ complete.            │  │  │                   │  │
│  │                      │  │  │                   │  │
│  └──────────────────────┘  │  └───────────────────┘  │
│  ┌──────────────────────┐  │                         │
│  │ Type a message...    │  │  3 pending · 2 done     │
│  └──────────────────────┘  │                         │
├────────────────────────────┴─────────────────────────┤
│                                              ⚡ Ollama │
└──────────────────────────────────────────────────────┘
```

### Chat Panel (Left, 60%)

- Scrollable message history
- User messages right-aligned, assistant messages left-aligned
- Text input at bottom — the only interactive element in the entire app
- Loading indicator while LLM processes
- Timestamps on messages (subtle)
- Auto-scrolls to newest message

### Todo Dashboard (Right, 40%)

- **Read-only** — no interactive elements
- Todos grouped by status: Pending first, then Done
- Each todo shows: title, tags (as chips), priority indicator if set, created date
- Summary bar: "N pending · M done"
- Updates reactively via Tauri events — never polled or manually refreshed

### Status Bar (Bottom)

- LLM provider indicator (e.g., "Ollama" or "OpenAI")
- Connection status dot (green = connected, red = unreachable)

---

## 5. Persistence

### SQLite Database

- Single database file in Tauri's `app_data_dir()`
- Two tables: `todos` and `messages`
- Schema created on first launch via `setup()` hook
- Simple version field for future migrations

### Schema

```sql
CREATE TABLE IF NOT EXISTS todos (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL,
    status TEXT NOT NULL DEFAULT 'pending',
    priority TEXT,
    tags TEXT,
    created_at TEXT NOT NULL,
    completed_at TEXT,
    updated_at TEXT NOT NULL
);

CREATE TABLE IF NOT EXISTS messages (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    role TEXT NOT NULL,
    content TEXT NOT NULL,
    timestamp TEXT NOT NULL
);
```

### Chat History

- All messages (user + assistant) are persisted
- On app launch, previous conversation loads into the chat panel
- Provides context for the LLM across sessions (the system prompt includes recent messages)

---

## 6. Tauri Integration Points

These are the specific Tauri features the app exercises:

| Tauri Feature | Implementation |
|---|---|
| **Commands** | `send_message(text) → ChatMessage`, `get_todos() → Vec<Todo>`, `get_chat_history() → Vec<ChatMessage>` |
| **Managed State** | DB connection pool + LLM client config registered via `manage()` |
| **Events** | `todos-updated` emitted after any todo mutation; frontend listens and refreshes dashboard |
| **App Lifecycle** | DB schema init in `setup()`, config loading |
| **Permissions** | HTTP access for LLM API, filesystem for DB |
| **Window Config** | Default size, title, min dimensions for the split layout |

---

## 7. Non-Functional Requirements

### Performance

- App launches and shows cached chat history + todos immediately
- LLM response time depends on provider (Ollama local ~1-3s for small models)
- UI remains responsive during LLM calls (async, non-blocking)

### Reliability

- Graceful fallback when LLM is unreachable (chat message, not a crash)
- Malformed LLM responses handled with retry prompt or user-friendly error
- Database is self-healing — missing DB triggers schema creation

### Security

- `.env` file with API keys is gitignored
- No sensitive data beyond what the user types as todos
- LLM API calls go only to the configured endpoint

### Scope Boundaries (MVP)

- Single LLM provider active at a time (no fallback chain)
- No undo/redo
- No todo due dates or reminders
- No export/import
- No multi-window or tray icon
- English only

### Future Considerations (out of scope)

- Due dates with natural language parsing ("remind me Thursday")
- Recurring todos
- Categories/projects beyond tags
- Voice input
- Mobile (Tauri 2 supports it, but out of scope for learning)
