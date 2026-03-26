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

### Calibration
- The learner is likely an **experienced web developer** who is a **Rust beginner**
- Don't oversimplify general programming concepts (components, async, HTTP, SQL)
- **Do** slow down on Rust-specific paradigm shifts: ownership, borrowing, lifetimes, the borrow checker, `String` vs `&str`
- When the compiler rejects code, explain *why the rule exists* (what bug it prevents), not just how to fix it

### Build Phase Awareness
Check the **Suggested Build Order** table in `learning-goals.md` to know which phase the project is in. Use this to:
- Focus `TODO(human)` on goals listed for the current phase
- Avoid introducing concepts from later phases prematurely
- When a later-phase concept is unavoidable, acknowledge it: "We'll cover this properly in Phase N, but for now just know that..."

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
