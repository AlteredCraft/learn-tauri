# Phase 7: LLM Client

## What You'll Build

A Rust HTTP client that communicates with Ollama's API. Send a prompt, receive a response, parse it into a Rust struct. This is a standalone module — not yet wired into the chat flow.

## Learning Goals

- **A6**: HTTP Client & Async
- **A7**: Serde & JSON
- **F4**: Error Handling (reinforcement)

## Prerequisites

- Phase 6 complete: events working, dashboard updates reactively
- Ollama installed and a model pulled (see `docs/llm-setup.md`)

## Getting Started

Create an `llm/` module directory. Start with the Ollama client: define request and response structs that match Ollama's API format, make an HTTP POST with `reqwest`, and parse the response.

Write an integration test that sends a simple prompt and verifies the response deserializes correctly. This test requires Ollama to be running — document this in a comment.

## Key Concepts

**Async in Rust**: HTTP calls are async — they return a `Future` that represents work in progress. Tauri uses `tokio` as its async runtime. Your commands can be `async fn`, and you use `.await` to wait for results. It's similar to `async/await` in JS, but the compiler enforces that you handle the future.

**reqwest**: The standard Rust HTTP client. It has both async and blocking APIs — use the async one. Request building is method-chained: `client.post(url).json(&body).send().await?`

**Serde for API integration**: Define Rust structs that mirror the API's JSON shape. Serde's `#[serde(rename_all = "camelCase")]` and `#[serde(default)]` attributes handle naming differences and optional fields. When the API response doesn't match your struct, serde returns an error — which you handle with `Result`.

## Done When

- An integration test sends a prompt to Ollama and gets a parsed response
- Request and response structs are defined with serde derives
- Errors (connection refused, timeout, malformed response) are handled with `Result`, not panics
- You can explain what `.await` does and why Rust requires it
- The LLM client is a separate module, not embedded in command handlers

## Hints (Expand if Stuck)

<details>
<summary>Hint 1: Ollama API format</summary>

Ollama's chat endpoint is `POST http://localhost:11434/api/chat`. The request body:
```json
{
    "model": "llama3.2:3b",
    "messages": [
        { "role": "system", "content": "..." },
        { "role": "user", "content": "..." }
    ],
    "stream": false
}
```
Set `stream: false` to get the full response at once instead of token-by-token.
</details>

<details>
<summary>Hint 2: reqwest setup</summary>

Add to `Cargo.toml`:
```toml
reqwest = { version = "0.12", features = ["json"] }
tokio = { version = "1", features = ["full"] }
```
</details>

<details>
<summary>Hint 3: Testing async code</summary>

Use `#[tokio::test]` for async test functions:
```rust
#[tokio::test]
async fn test_ollama_chat() {
    // This test requires Ollama to be running
    let response = send_prompt("Say hello").await;
    assert!(response.is_ok());
}
```
</details>
