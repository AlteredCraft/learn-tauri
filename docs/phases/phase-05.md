# Phase 5: Chat Interface

## What You'll Build

The real UI: a split-panel layout with a chat panel (left, 60%) and a read-only todo dashboard (right, 40%). The chat works in "echo mode" for now — messages are displayed but there's no LLM processing yet.

## Learning Goals

- **A8**: Svelte 5 Reactivity & Runes
- **A9**: Svelte Component Architecture
- **A10**: Tauri Frontend Integration

## Prerequisites

- Phase 4 complete: Tauri commands working, temporary CRUD UI functional

## Getting Started

Replace the temporary UI from Phase 4 with the split-panel layout. Decompose it into components: `ChatPanel`, `ChatMessage`, `ChatInput`, `TodoDashboard`, and `StatusBar`.

For now, the chat just echoes: when the user submits a message, save it via a Tauri command and display a placeholder assistant response ("Echo: [your message]"). The real LLM integration comes in Phase 7-8.

## Key Concepts

**Svelte 5 runes**: Svelte 5 introduced runes for reactivity:
- `$state` — reactive variable (like React's `useState`)
- `$derived` — computed value that updates when dependencies change
- `$effect` — side effect that runs when dependencies change (like `useEffect`)

If you're coming from React, the mental model is similar but the syntax is cleaner. If you're coming from Vue, Svelte's reactivity is compile-time rather than runtime.

**Component architecture**: Each visual section becomes its own `.svelte` file. Data flows down via props; events flow up via callbacks or Svelte's event system. Keep components focused — a `ChatMessage` renders one message, a `ChatPanel` manages the list.

**Tauri integration from Svelte**: Import `invoke` from `@tauri-apps/api/core` to call Rust commands. Import `listen` from `@tauri-apps/api/event` to subscribe to Tauri events (you'll use this in Phase 6).

## Done When

- Split-panel layout renders: chat on left, todo dashboard on right
- Typing a message and pressing Enter adds it to the chat
- An echo response appears below the user's message
- Todo dashboard shows current todos (from `get_todos` command)
- Components are cleanly separated (at least 4 `.svelte` files)
- You can explain what `$state` and `$derived` do

## Hints (Expand if Stuck)

<details>
<summary>Hint 1: CSS layout</summary>

CSS Grid or Flexbox both work for the split panel. A simple approach:
```css
.layout {
    display: grid;
    grid-template-columns: 60fr 40fr;
    height: 100vh;
}
```
</details>

<details>
<summary>Hint 2: Auto-scrolling the chat</summary>

Use `$effect` to scroll to the bottom of the chat panel whenever the message list changes. You'll need a reference to the scroll container — Svelte's `bind:this` gives you the DOM element.
</details>

<details>
<summary>Hint 3: Loading chat history</summary>

Call `invoke("get_chat_history")` when the component mounts (inside an `$effect` with no dependencies, or in an `onMount` callback). Store the result in a `$state` variable.
</details>
