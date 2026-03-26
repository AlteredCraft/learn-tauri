# Phase 1: Explore the Scaffold + Dev Workflow

## What You'll Do

The Tauri 2 + Svelte 5 project is already scaffolded. Your job in this phase is to get it running, explore every file, and understand how the pieces connect before writing any code.

## Learning Goals

- **A1**: Tauri App Scaffolding & Lifecycle
- **F6**: Modules & Crates

## Prerequisites

- Rust toolchain installed (`rustc --version`)
- Node.js 18+ and pnpm installed
- Tauri prerequisites for your platform (see [prerequisites](https://v2.tauri.app/start/prerequisites/))

## Getting Started

```bash
pnpm install
pnpm tauri dev
```

A window should open. Once it does, your first task is to explore every file and understand what it does. Start with these questions:

- What's in `src-tauri/` vs `src/`? Which side is Rust, which is web?
- What does `tauri.conf.json` configure? What happens if you change the window title?
- What does `Cargo.toml` do? How does it compare to `package.json`?
- Open `src-tauri/src/lib.rs` — what does `Builder::default()` do? What is `generate_context!()`?
- Open `src/routes/+page.svelte` — how does it call Rust? What is `invoke()`?
- Why does `+layout.ts` set `ssr = false`?

## Key Concepts

**Tauri architecture**: Tauri apps have two sides. The Rust backend (`src-tauri/`) handles system access, business logic, and native capabilities. The web frontend (`src/`) is rendered in a webview — think of it as a browser tab that can talk to Rust. They communicate over IPC, not HTTP.

```
┌──────────────────────────────────┐
│         Tauri Window             │
│                                  │
│  ┌────────────────────────────┐  │
│  │  Svelte Frontend (webview) │  │
│  │  invoke("greet", {name})   │──┼──► IPC bridge
│  └────────────────────────────┘  │        │
│                                  │        ▼
│  ┌────────────────────────────┐  │
│  │  Rust Backend (native)     │  │
│  │  #[tauri::command]         │  │
│  │  fn greet(name: &str)      │  │
│  └────────────────────────────┘  │
└──────────────────────────────────┘
```

**The build pipeline**: `pnpm tauri dev` starts both the Vite dev server (for Svelte) and the Rust compiler (via Cargo). Changes to Svelte files hot-reload instantly; changes to Rust files trigger a recompile (~2-5 seconds).

**Cargo.toml**: Rust's equivalent of `package.json`. Dependencies, project metadata, and build configuration live here. The `[lib]` section defines the crate name; the `[dependencies]` section lists what you're building on.

**main.rs vs lib.rs**: Rust separates the binary entry point (`main.rs`) from the library code (`lib.rs`). The binary is a thin wrapper; the library is what Tauri links against. This matters for testing — you can test library code without running the full app.

## Done When

- `pnpm tauri dev` opens a window with "NatLang Todo" in the title bar
- The demo greet command works (type a name, see the response)
- You can explain what each file in the project does — ask Claude Code to quiz you
- `cargo test` (in `src-tauri/`) passes
- You can trace the full journey of `invoke("greet", { name })` from Svelte through IPC to Rust and back

## Hints (Expand if Stuck)

<details>
<summary>Hint 1: First compile is slow</summary>

The first `pnpm tauri dev` downloads and compiles all Rust dependencies. This can take 3-5 minutes. Subsequent runs are fast (incremental compilation). If you want to check just the Rust side: `cd src-tauri && cargo check`.
</details>

<details>
<summary>Hint 2: Window configuration</summary>

Look in `src-tauri/tauri.conf.json` for window settings. Try changing the title or dimensions and see the effect on next launch. The `capabilities/` directory controls what the webview is allowed to do (permissions).
</details>

<details>
<summary>Hint 3: Tracing the IPC bridge</summary>

The demo app has a `greet` command. Trace it end-to-end:
1. `src/routes/+page.svelte` — calls `invoke("greet", { name })`
2. Tauri routes the call to Rust based on the command name
3. `src-tauri/src/lib.rs` — `#[tauri::command] fn greet(name: &str) -> String`
4. The return value is serialized to JSON and sent back to Svelte
</details>
