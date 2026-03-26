# Phase 1: Tauri Scaffold + Dev Workflow

## What You'll Build

A Tauri 2 + Svelte 5 project that compiles and opens an empty window. The dev workflow works: `pnpm tauri dev` launches the app with hot reload.

## Learning Goals

- **A1**: Tauri App Scaffolding & Lifecycle
- **F6**: Modules & Crates

## Prerequisites

- Rust toolchain installed (`rustc --version`)
- Node.js 18+ and pnpm installed
- Tauri prerequisites for your platform (see [prerequisites](https://v2.tauri.app/start/prerequisites/))

## Getting Started

Ask Claude Code to help you create a new Tauri 2 project with Svelte 5. The key command is `pnpm create tauri-app` — but understand what each choice means before accepting defaults.

After scaffolding, explore the file structure. Understand:
- What's in `src-tauri/` vs `src/`?
- What does `tauri.conf.json` configure?
- What does `Cargo.toml` do?
- How do the Rust backend and Svelte frontend connect?

## Key Concepts

**Tauri architecture**: Tauri apps have two sides. The Rust backend (`src-tauri/`) handles system access, business logic, and native capabilities. The web frontend (`src/`) is rendered in a webview — think of it as a browser tab that can talk to Rust.

**The build pipeline**: `pnpm tauri dev` starts both the Vite dev server (for Svelte) and the Rust compiler (via Cargo). Changes to Svelte files hot-reload; changes to Rust files trigger a recompile.

**Cargo.toml**: Rust's equivalent of `package.json`. Dependencies, project metadata, and build configuration live here.

## Done When

- `pnpm tauri dev` opens a window with "NatLang Todo" in the title bar
- The window displays placeholder text (e.g., "Hello from NatLang Todo")
- You can explain what each file in the project does
- `cargo test` (in `src-tauri/`) passes (even if there are no tests yet)

## Hints (Expand if Stuck)

<details>
<summary>Hint 1: Scaffolding choices</summary>

When running `pnpm create tauri-app`, choose:
- Project name: `natlang-todo`
- Frontend: Svelte
- TypeScript: Yes
- Package manager: pnpm

Then move the generated files into this repo's root (since you already have CLAUDE.md and other framework files here).
</details>

<details>
<summary>Hint 2: Window configuration</summary>

Look in `src-tauri/tauri.conf.json` for window settings. You can set the title, default width/height, and minimum dimensions here. A good starting size for the split-panel layout is 1000x700.
</details>

<details>
<summary>Hint 3: Verifying the connection</summary>

Add a simple Tauri command that returns a string, call it from Svelte, and display the result. This proves the IPC bridge works before you build anything real.
</details>
