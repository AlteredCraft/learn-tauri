# Phase 2: Data Model in Rust

## What You'll Build

A standalone Rust module defining the app's data types: `Todo`, `TodoStatus`, `ChatMessage`, and `LlmIntent`. No database, no UI — pure Rust data modeling with unit tests.

## Learning Goals

- **F1**: Ownership & Borrowing
- **F2**: Structs & Enums as Data Modeling
- **F3**: Pattern Matching
- **F5**: Traits
- **F7**: String Handling (String vs &str)

## Prerequisites

- Phase 1 complete: Tauri project compiles, dev workflow works

## Getting Started

Create a `models.rs` file in `src-tauri/src/`. Define the data types from the spec (Section 2). Write unit tests that create instances, serialize them, and pattern-match on enum variants.

This is the most foundational Rust learning phase. Take your time — the concepts here (ownership, enums with data, pattern matching) are the mental model shifts that make everything else click.

## Key Concepts

**Structs vs classes**: Rust structs hold data but have no inheritance. Methods are attached via `impl` blocks. If you're coming from JS/TS, think of them as typed object literals with associated functions.

**Enums with data**: Rust enums aren't just constants — each variant can carry different data. `LlmIntent::Add { title: String }` is fundamentally different from TypeScript's union types because the compiler enforces exhaustive matching.

**Ownership**: When you pass a `String` to a function, the function *takes ownership* and the caller can't use it anymore. This is Rust's core innovation — it prevents data races and use-after-free at compile time. You'll fight the compiler here. That's the learning.

## Done When

- `cargo test` passes with tests covering:
  - Creating `Todo` and `ChatMessage` instances
  - Pattern matching on `TodoStatus` and `LlmIntent` variants
  - Serialization/deserialization with serde
- You can explain the difference between `String` and `&str`
- You can explain why `match` on an enum must cover all variants

## Hints (Expand if Stuck)

<details>
<summary>Hint 1: Deriving traits</summary>

Most of your structs will want `#[derive(Debug, Clone, Serialize, Deserialize)]`. Don't forget to add `serde` as a dependency in `Cargo.toml` with the `derive` feature.
</details>

<details>
<summary>Hint 2: LlmIntent enum design</summary>

Each variant should carry the data needed to execute that operation. For example:
```rust
enum LlmIntent {
    Add { title: String, priority: Option<String>, tags: Option<Vec<String>> },
    Complete { identifier: String },
    // ... etc
}
```
The `Option<T>` type is how Rust handles nullable values — there's no `null`.
</details>

<details>
<summary>Hint 3: Testing pattern matching</summary>

Write a test that creates an `LlmIntent::Add` and uses `match` to extract the title. The compiler will force you to handle every variant — try leaving one out and see what happens.
</details>
