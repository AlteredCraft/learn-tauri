# Phase 9: Remove Training Wheels + Polish

## What You'll Build

Remove the temporary CRUD controls from Phase 4. The chat interface is now the only way to interact. Add chat history persistence (conversation loads on app launch). Visual polish: loading indicator, error messages in chat, auto-scroll, timestamps.

## Learning Goals

- **A8**: Svelte 5 Reactivity & Runes (reinforcement)
- **A9**: Svelte Component Architecture (reinforcement)

## Prerequisites

- Phase 8 complete: NL pipeline working end-to-end

## Getting Started

Delete the temporary UI components from Phase 4. Load chat history on app launch so previous conversations persist across sessions. Add polish that makes the app feel complete.

## Key Concepts

**The moment of truth**: Removing the traditional controls is psychologically significant. The app now relies entirely on the system you built: chat input → LLM classification → Rust execution → event-driven update. If something doesn't work, you can't fall back to clicking a button.

**Chat history as context**: Loading previous messages isn't just for display — the conversation history helps you (and potentially the LLM) maintain context. Consider how many recent messages to include in the LLM's context window.

**Loading states**: LLM calls take 1-3 seconds with Ollama. The UI should show the user something is happening — a loading indicator, a typing animation, or a disabled input. Use Svelte's reactive state to toggle this.

## Done When

- No buttons, checkboxes, or form inputs visible (except the chat text input)
- Previous chat history loads when the app opens
- Loading indicator shows during LLM processing
- Error messages appear as chat messages, not alerts or console errors
- Chat auto-scrolls to the newest message
- The app feels like a coherent product, not a prototype

## Hints (Expand if Stuck)

<details>
<summary>Hint 1: Loading history on mount</summary>

In your main page component, use `$effect` or `onMount` to call `invoke("get_chat_history")` and populate the message list. Handle the case where there's no history (first launch).
</details>

<details>
<summary>Hint 2: Loading state</summary>

Track whether a message is being processed with a `$state` boolean. When true: disable the input, show an indicator in the chat. Toggle it before and after the `invoke("send_message")` call.
</details>

<details>
<summary>Hint 3: Graceful error display</summary>

If the LLM is unreachable, the Rust command should return a `ChatMessage` with an error-like content ("I couldn't reach the LLM — is Ollama running?") rather than throwing. The frontend treats it like any other assistant message.
</details>
