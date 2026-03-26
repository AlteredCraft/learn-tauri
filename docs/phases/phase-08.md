# Phase 8: Intent Classification + NL Pipeline

## What You'll Build

The core natural language pipeline. Construct a system prompt that tells the LLM what operations are available and what JSON format to return. Send the user's message plus todo context. Parse the structured response into an `LlmIntent` enum. Dispatch to the correct DB operation. Return a human-friendly chat message.

This is the phase where the app becomes what it's supposed to be.

## Learning Goals

- **A11**: LLM Prompt Engineering in Code
- **F3**: Pattern Matching (reinforcement)
- **F4**: Error Handling (reinforcement)

## Prerequisites

- Phase 7 complete: LLM client can send prompts and parse responses
- Phase 3-4: DB CRUD functions working

## Getting Started

Create a `prompt.rs` file in the `llm/` module. Build the system prompt as a Rust string that includes:
1. The list of available operations with their parameters
2. The expected JSON output format (with schema)
3. The current todo list (so the LLM can resolve references like "the milk one")

Wire the full pipeline into the `send_message` Tauri command: save user message → build prompt → call LLM → parse intent → execute DB operation → construct response → save assistant message → emit event → return response.

## Key Concepts

**Prompt engineering in code**: The system prompt isn't a static string — it's constructed dynamically from the current application state. The todo list is injected as context so the LLM can resolve references. The operation schema is defined once and included in every request.

**Structured output**: The LLM returns JSON that maps to your `LlmIntent` enum. The system prompt must be explicit about the expected format. Different models handle structured output with different reliability — build fallback logic for when the JSON is malformed.

**The dispatch pattern**: After parsing the LLM's response into an `LlmIntent`, use `match` to dispatch to the correct DB function. This is where the enum + pattern matching design from Phase 2 pays off — every intent variant gets handled, the compiler ensures nothing is missed.

## Done When

- Typing "add buy milk" in the chat creates a todo and shows a confirmation
- Typing "show all todos" returns a formatted list
- Typing "mark buy milk as done" updates the todo status
- Typing "delete milk" removes the todo
- Malformed LLM responses don't crash the app — they produce a helpful error message in chat
- You can explain how the system prompt is constructed and why it includes the current todo list

## Hints (Expand if Stuck)

<details>
<summary>Hint 1: System prompt structure</summary>

Your system prompt should include:
1. Role description: "You are a todo assistant that classifies user intents"
2. Available operations with expected JSON format for each
3. Current todos as context (so the LLM can match "milk" to "buy milk")
4. Strict instruction to respond with JSON only, no explanation
</details>

<details>
<summary>Hint 2: Parsing the LLM response</summary>

The LLM returns text, not typed data. You need to:
1. Extract the JSON from the response (some models wrap it in markdown code fences)
2. Deserialize it with serde
3. Handle failures — if serde fails, try cleaning the response (strip code fences, trim whitespace)
4. If parsing still fails, return an `LlmIntent::Unclear` with the raw text
</details>

<details>
<summary>Hint 3: The match dispatch</summary>

```rust
match intent {
    LlmIntent::Add { title, priority, tags } => {
        db::add_todo(&conn, &title, priority.as_deref(), tags.as_deref())?;
        format!("Added '{}' to your todos.", title)
    }
    LlmIntent::Complete { identifier } => {
        // find the todo, mark it done
    }
    // ... handle all variants
}
```
</details>
