---
name: project-overview
description: Give the learner an orientation to the NatLang Todo project — what they're building, why, what Tauri is and how it works, and what they'll learn. Run when the learner first opens the project or asks "what is this?" / "what am I building?"
---

You are orienting a developer who is about to learn Tauri 2, Svelte 5, and Rust by building a natural-language todo app. Present this as a conversation, not a wall of text. Use the architecture diagram from `docs/architecture.md` and the build phase table from `learning-goals.md` to make it concrete.

## Structure

Present the overview in this order. Keep each section concise — the learner can ask follow-ups.

### 1. What You're Building

A desktop todo app where the **only way to manage your todos is by typing natural language**. No buttons, no checkboxes, no forms. You type "add buy milk" and a local LLM figures out you want to add a todo. Rust does all the real work.

Show the UI layout from `spec.md` Section 4 (the ASCII mockup) — a split panel with chat on the left, read-only todo dashboard on the right.

Walk through one interaction end-to-end: "add buy milk" → what happens at each layer. Use the data flow from `docs/architecture.md`.

### 2. Why This App

Explain why a natural-language todo app is a better learning vehicle than a traditional CRUD todo app:
- Traditional todo: 90% of the logic is frontend (forms, click handlers, state). The backend is a boring pass-through.
- NatLang todo: 70% of the logic is in Rust — LLM orchestration, intent parsing, database operations, error handling, event emission. The frontend is thin.
- This means you spend your time where the Tauri and Rust concepts actually live.

### 3. What Is Tauri (and Why Learn It)

Explain Tauri for someone who knows web dev but has never built a desktop app:

- **The problem Tauri solves**: You know HTML/CSS/JS. You want to build a desktop app. Electron bundles an entire Chromium browser (300MB+). Tauri uses the OS's built-in webview instead (~5MB), with a Rust backend for performance and system access.
- **The two-process model**: A Rust backend (native binary, owns the window and system resources) + a web frontend (runs in a webview, like a browser tab that can talk to Rust). They communicate over IPC.
- **The key building blocks** the learner will use in this project:
  - **Commands** — Expose Rust functions to JavaScript. The frontend calls `invoke("send_message", { text })`, Tauri routes it to Rust, Rust returns a result. This is the primary communication pattern.
  - **Managed State** — Share data (like a database connection) across commands without global variables. Tauri holds it and injects it into command handlers.
  - **Events** — Push notifications from Rust to the frontend. After Rust changes a todo, it emits `"todos-updated"` and the dashboard refreshes. Unlike commands (pull), events are push.
  - **App Lifecycle** — `setup()` runs on launch (initialize the database), the event loop runs the app, `on_exit()` cleans up.
  - **Permissions** — Tauri's security model controls what the webview can access (HTTP endpoints, filesystem paths). You explicitly allow what's needed.
- **How it compares to what they know**: If they've built a React/Vue/Svelte web app with a REST API backend, Tauri is similar — except the "backend" is compiled Rust running on the same machine, and "API calls" are typed IPC instead of HTTP.

Link to the Tauri 2 docs: https://v2.tauri.app/

### 4. What You'll Learn

Read `learning-goals.md` and present the two categories:

**Rust fundamentals (F1-F7)**: Ownership & borrowing, structs & enums, pattern matching, error handling, traits, modules, string handling. These are the mental model shifts — the concepts that make Rust different from JS/Python.

**Applied skills (A1-A11)**: Tauri scaffolding, SQLite, Tauri commands, managed state, events, async HTTP, serde/JSON, Svelte 5 runes, component architecture, Tauri frontend integration, LLM prompt engineering.

Show the build phase table from `learning-goals.md` — 10 phases, each producing something testable, graduating from simple to complex.

### 5. How the Learning Framework Works

Briefly explain the three layers (don't over-explain — they'll experience them):
- **Learning output style**: Claude Code writes scaffolding but leaves `TODO(human)` markers for you to implement. Each marker references a learning goal ID.
- **SessionStart hook**: Every time you open Claude Code, it shows your current phase and which goals are next.
- **`/checkpoint` skill**: At the end of a session, run this to reflect on what clicked and what's fuzzy. Your notes are saved for continuity.

### 6. Close with Next Steps

After the overview, suggest:
- If dependencies aren't installed yet (no `node_modules/`): "Ready to start? I'll run a preflight check on your environment, then you can `pnpm install && pnpm tauri dev` to launch the app."
- If the app is running but Phase 1 goals are unchecked: "The scaffold is ready — Phase 1 is about exploring the project structure and understanding how Tauri connects Rust to the webview. Start with `docs/phases/phase-01.md`."
- If Phase 1 is done: "Check `learning-goals.md` to see which phase you're on, or ask me what's next."

## Tone

Peer-to-peer. The learner is an experienced developer learning a new stack, not a beginner learning to code. Be direct, skip the cheerleading, focus on the "why" behind each piece.

End by asking if they have questions about the architecture or approach before diving in.
