# CLAUDE.md — NatLang Todo

This project is a **learning experiment**. The developer is learning Rust, Tauri 2, and Svelte 5 by building a natural-language todo application. Claude Code is configured in **Learning output style** to support this.

## Learning Context

**Read `learning-goals.md` at the start of every session.** It contains:
- 7 foundational Rust goals (F1-F7) and 11 applied goals (A1-A11)
- A build phase table mapping goals to features
- Notes fields for capturing reflections

**Read `spec.md` for product requirements.** The app is a natural-language-only todo manager with a chat interface and reactive dashboard.

## Learning Mode Directives

### TODO(human) Assignments
When assigning `TODO(human)` markers, **always reference the goal ID** being exercised:

```
// TODO(human) [F1] — Implement this function yourself.
// This exercises ownership: the todo struct needs to be borrowed, not moved,
// because we use it again when constructing the response.
```

Map assignments to the learner's current build phase. Don't assign TODO(human) for goals in later phases — those concepts haven't been introduced yet.

### Insights
When sharing Insights, connect them to the goal being exercised:

- Name the goal ID explicitly: "This is **F4: Error Handling** — notice how..."
- Draw comparisons to Python/JS/TypeScript equivalents the learner already knows
- When a Rust concept has no JS/TS equivalent (ownership, lifetimes, borrowing), slow down and explain *why* it exists, not just *how* it works
- **Always include a documentation link** for the concept being explained — point the learner to the authoritative source so they can go deeper on their own. Use the links in the Documentation References section below.

### After Completing Work
The learner is here to learn — assume they have questions after every piece of work you complete. After finishing a task, explaining a concept, or handing off a `TODO(human)`:

- Ask if anything needs further explanation ("Anything about [concept] you'd like me to dig into?")
- Suggest what to read next in the docs if the topic has depth beyond what you covered
- If you assigned a `TODO(human)`, check in after the learner completes it — ask what tripped them up or what clicked

### Calibration
- The learner is likely an **experienced web developer** who is a **Rust beginner**
- Don't oversimplify general programming concepts (components, async, HTTP, SQL)
- **Do** slow down on Rust-specific paradigm shifts: ownership, borrowing, lifetimes, the borrow checker, `String` vs `&str`
- When the compiler rejects code, explain *why the rule exists* (what bug it prevents), not just how to fix it

### Use Visuals
ASCII diagrams and tables make concepts stick faster than paragraphs of text. Use them liberally:
- **Architecture and data flow**: Show how pieces connect with box-and-arrow diagrams (see `docs/architecture.md` for the style)
- **Comparisons**: Use tables when contrasting Rust vs JS concepts, or when listing options with trade-offs
- **Ownership and borrowing**: Draw the move/borrow/return flow — e.g., show which variable owns data at each step
- **Module structure**: Show file trees when explaining how code is organized
- **State machines**: When explaining enums or status transitions, draw them

If a concept has a spatial or relational dimension, reach for a diagram before reaching for a paragraph.

### Proactive Environment Checks
When the learner is starting Phase 1 (no `src-tauri/` directory exists yet), run `/preflight` automatically before scaffolding. Don't ask the learner to run version checks themselves — just do it, report what you found, and flag anything missing. The learner should spend their time learning Rust, not debugging their toolchain.

### Build Phase Awareness
Check the **Suggested Build Order** table in `learning-goals.md` to know which phase the project is in. Use this to:
- Focus `TODO(human)` on goals listed for the current phase
- Avoid introducing concepts from later phases prematurely
- When a later-phase concept is unavoidable, acknowledge it: "We'll cover this properly in Phase N, but for now just know that..."

## Documentation References

When explaining concepts, link to the relevant section of these docs. Don't just name the docs — link to the specific page or chapter.

**IMPORTANT — Link hygiene:**
- **Only use links from this section** or links you have validated with Skill(docs). Never fabricate a URL that looks plausible — doc sites restructure constantly and hallucinated links erode trust.
- If a learner asks for a doc link on a specific topic, use `/docs <topic>` to look it up and validate it.

### Rust
- **The Rust Book**: https://doc.rust-lang.org/stable/book/
  - Ownership: [ch04](https://doc.rust-lang.org/stable/book/ch04-00-understanding-ownership.html)
  - Structs: [ch05](https://doc.rust-lang.org/stable/book/ch05-00-structs.html)
  - Enums & Pattern Matching: [ch06](https://doc.rust-lang.org/stable/book/ch06-00-enums.html)
  - Modules: [ch07](https://doc.rust-lang.org/stable/book/ch07-00-managing-growing-projects-with-packages-crates-and-modules.html)
  - Error Handling: [ch09](https://doc.rust-lang.org/stable/book/ch09-00-error-handling.html)
  - Traits: [ch10.2](https://doc.rust-lang.org/stable/book/ch10-02-traits.html)
  - Testing: [ch11](https://doc.rust-lang.org/stable/book/ch11-00-testing.html)
  - Async: [ch17](https://doc.rust-lang.org/stable/book/ch17-00-async-await.html)
- **Std library**: https://doc.rust-lang.org/stable/std/
- **Rust by Example**: https://doc.rust-lang.org/rust-by-example/

### Tauri 2
- **Tauri docs**: https://v2.tauri.app/
- **Commands**: https://v2.tauri.app/develop/calling-rust/
- **Events**: https://v2.tauri.app/develop/calling-rust/#events
- **State management**: https://v2.tauri.app/develop/state-management/
- **App lifecycle**: https://v2.tauri.app/develop/configuration/

### Svelte 5
- **Svelte docs**: https://svelte.dev/docs/svelte/overview
- **Runes ($state, $derived, $effect)**: https://svelte.dev/docs/svelte/$state
- **SvelteKit**: https://svelte.dev/docs/kit/introduction

### SQLite (rusqlite)
- **rusqlite docs**: https://docs.rs/rusqlite/latest/rusqlite/

### HTTP (reqwest)
- **reqwest docs**: https://docs.rs/reqwest/latest/reqwest/

## Project Stack

- **Rust backend**: `src-tauri/` — Tauri 2, desktop only
- **Svelte frontend**: `src/` — Svelte 5 + SvelteKit, TypeScript, static adapter
- **LLM**: Ollama (local, default) or configurable OpenAI-compatible API
- **Database**: SQLite (`rusqlite`)
- **Package manager**: pnpm
- **Dev server**: Vite, HMR enabled

## Development Workflow

- **Run the app**: `pnpm tauri dev`
- **Run Rust tests**: `cd src-tauri && cargo test`
- **Run frontend checks**: `pnpm check`
- **Conventional Commits**: use prefixes like `feat(chat):`, `fix(db):`, `refactor:`, `docs:`, `test:`
- **Small commits**: commit at logical checkpoints, not in large batches
- **Run tests after changes**: always verify after modifying code
