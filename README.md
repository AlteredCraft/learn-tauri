# NatLang Todo

Learn **Tauri 2 + Svelte 5 + Rust** by building a todo app with a natural-language-only interface.

No buttons. No checkboxes. No forms. You type what you want, an LLM figures out your intent, and Rust makes it happen.

## What this is

This is not a tutorial you copy-paste. It's a **learning framework** that reconfigures [Claude Code](https://claude.ai/code) into a teaching system.

When you open this project in Claude Code, it activates **Learning output style** — a mode where AI writes the surrounding code but leaves strategic gaps marked `TODO(human)` for you to implement yourself. Each gap is tied to a specific learning goal, so you're always building understanding, not just building software.

### The anti-brain-rot protocol

The framework has three layers that work together:

1. **Learning output style** — `TODO(human)` markers at decision points + Insight blocks that explain concepts in context
2. **CLAUDE.md as curriculum** — Directs Claude to tie every assignment to your current learning phase and goals
3. **SessionStart hook + /checkpoint skill** — Injects your learning state into every session and captures reflections at the end

You build the entire app from scratch across 10 progressive phases. The framework guides, but you do the work.

## What you'll build

A desktop todo app where the **only interface is a chat window**. You type natural language ("add buy milk", "mark groceries as done", "show everything tagged work"), and the app:

1. Sends your message to a local LLM (Ollama)
2. The LLM classifies your intent and returns structured JSON
3. Rust parses the intent and executes against SQLite
4. A Tauri event fires, and the Svelte dashboard updates reactively

The UI is a split panel: chat on the left, read-only todo list on the right.

## Why this app

Traditional todo apps put 90% of the logic in the frontend. This design inverts it — 70% of the interesting work happens in Rust (LLM orchestration, intent parsing, database ops, error handling, event emission). You spend your time where the learning goals are.

## Prerequisites

- **Rust toolchain**: [rustup.rs](https://rustup.rs)
- **Node.js 18+** and **pnpm**: `npm install -g pnpm`
- **Ollama**: [ollama.com](https://ollama.com) — pull a model: `ollama pull llama3.2:3b`
- **Claude Code**: [claude.ai/code](https://claude.ai/code) with a subscription
- **Tauri prerequisites**: See [Tauri prerequisites](https://v2.tauri.app/start/prerequisites/)

## Quick start

1. Fork this repo and clone it
2. `pnpm install`
3. Copy `.env.example` to `.env` (defaults work if Ollama is installed)
4. Open the project in Claude Code
5. `pnpm tauri dev` — verify the app window opens
6. Read `docs/phases/phase-01.md` for your first task: exploring the scaffold

## Project structure

```
├── README.md              ← You are here
├── spec.md                ← What the app does (product spec)
├── learning-goals.md      ← What you're learning (F1-F7, A1-A11)
├── CLAUDE.md              ← Curriculum directives for Learning mode
├── .claude/
│   ├── settings.json      ← Learning output style + hooks
│   └── skills/
│       ├── checkpoint/    ← End-of-session reflection
│       ├── verify/        ← Run clippy + tests + checks
│       ├── preflight/     ← Check prerequisites are installed
│       ├── project-overview/ ← Orientation: what you're building + why
│       └── docs/          ← Look up validated documentation links
├── .env.example           ← LLM provider config
├── src/                   ← Svelte 5 frontend (pre-scaffolded)
├── src-tauri/             ← Rust backend (pre-scaffolded)
└── docs/
    ├── architecture.md    ← System architecture diagram
    ├── llm-setup.md       ← Ollama installation guide
    └── phases/            ← Per-phase briefing docs (phase-01 through phase-10)
```

The Tauri app is pre-scaffolded with a working demo command. Phase 1 is about exploring and understanding the structure — not creating it from scratch.

## How it works

### During a session

The **SessionStart hook** fires when you open Claude Code, showing your current build phase and which learning goals are next. As you work, Claude Code operates in Learning mode — writing scaffolding, leaving `TODO(human)` markers for you, and providing Insight blocks that explain Rust/Tauri/Svelte concepts.

### At the end of a session

Run `/checkpoint` to trigger an end-of-session reflection. It maps your git changes to learning goals, asks what clicked and what's fuzzy, and saves your responses to `learning-goals.md` for continuity.

### Across sessions

The hook re-injects your learning state each time, so Claude Code always knows where you are in the build. Your notes accumulate in `learning-goals.md`, building a record of your learning journey.

## FAQ

**Can I use a different LLM?**
Yes. The app supports any OpenAI-compatible API. Set `LLM_PROVIDER=openai-compatible` in `.env` and configure the base URL and API key. Ollama is the default because it's free and local.

**What if I'm stuck?**
Each phase doc has expandable hints. You can also ask Claude Code to explain a concept without generating code — it'll reference the relevant learning goal.

**How long does this take?**
Depends on your Rust experience. A web developer new to Rust might spend 2-4 sessions per phase. The point isn't speed — it's understanding.

**Do I need to know Svelte?**
Basic web dev knowledge is enough. Svelte 5 specifics (runes, component patterns) are covered in the learning goals. Claude Code will draw comparisons to React/Vue if that helps.

## License

MIT
