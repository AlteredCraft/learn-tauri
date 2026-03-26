# Phase 10: Multi-Provider Support

## What You'll Build

Abstract the LLM client behind a Rust trait. Implement a second provider (OpenAI-compatible API). The app reads `.env` to determine which provider to use. Switching providers requires no code changes — just configuration.

## Learning Goals

- **A6**: HTTP Client & Async (extension)
- **F5**: Traits (the big payoff)

## Prerequisites

- Phase 9 complete: app is fully functional with Ollama

## Getting Started

Define an `LlmProvider` trait with a method like `async fn chat(&self, messages: Vec<Message>) -> Result<String, LlmError>`. Your existing Ollama client becomes one implementation. Create a second implementation for the OpenAI-compatible chat completions API.

Use the `.env` configuration to select which provider to instantiate at startup. Register the chosen provider as Tauri managed state.

## Key Concepts

**Traits as real abstraction**: Up to now, you've used traits mostly through derives (Serialize, Debug, Display). In this phase, you define your own trait — the Rust equivalent of an interface. The `LlmProvider` trait describes what any LLM client must do, without specifying how. Each provider implements it differently.

**Trait objects vs generics**: You have two options for using your trait: generics (`fn foo<T: LlmProvider>(provider: T)`) or trait objects (`fn foo(provider: &dyn LlmProvider)`). For managed state in Tauri, you'll likely use trait objects (`Box<dyn LlmProvider>`) because the concrete type is determined at runtime from configuration.

**The OpenAI API format**: The OpenAI-compatible format uses a different endpoint (`/v1/chat/completions`) and different JSON structure than Ollama. Both do the same thing — send messages, get a response. The trait hides this difference from the rest of your code.

## Done When

- An `LlmProvider` trait is defined with at least one method
- Two implementations exist: Ollama and OpenAI-compatible
- Changing `LLM_PROVIDER` in `.env` switches the active provider
- The app works identically with both providers (assuming the model is capable)
- You can explain what a trait is and why it's useful here
- You can explain the difference between generics and trait objects

## Hints (Expand if Stuck)

<details>
<summary>Hint 1: Trait definition</summary>

```rust
#[async_trait]
pub trait LlmProvider: Send + Sync {
    async fn chat(&self, system_prompt: &str, user_message: &str) -> Result<String, LlmError>;
}
```
You'll need the `async-trait` crate because Rust doesn't natively support async methods in traits yet (it's stabilizing, but `async-trait` is still the common pattern).
</details>

<details>
<summary>Hint 2: OpenAI chat completions format</summary>

```json
POST /v1/chat/completions
{
    "model": "gpt-4o-mini",
    "messages": [
        { "role": "system", "content": "..." },
        { "role": "user", "content": "..." }
    ]
}
```
The response has the completion nested at `response.choices[0].message.content`.
</details>

<details>
<summary>Hint 3: Provider selection at startup</summary>

In your Tauri setup, read the `.env` config and instantiate the right provider:
```rust
let provider: Box<dyn LlmProvider> = match config.provider.as_str() {
    "ollama" => Box::new(OllamaClient::new(config.base_url, config.model)),
    "openai-compatible" => Box::new(OpenAiClient::new(config.base_url, config.api_key, config.model)),
    _ => panic!("Unknown LLM provider"),
};
app.manage(provider);
```
</details>

## What's Next

Congratulations — you've built a complete Tauri 2 application with Rust, Svelte, and LLM integration. Run `/checkpoint` one last time to capture your final reflections.

Consider these extensions if you want to keep going:
- **Due dates**: "remind me to call the dentist Thursday" with natural language date parsing
- **Recurring todos**: "add a weekly team standup"
- **Voice input**: Web Speech API in the webview, transcribed to text
- **Mobile**: Tauri 2 supports iOS and Android — try building for mobile
- **Better models**: Try larger models and compare classification accuracy
