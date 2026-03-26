---
name: preflight
description: Check that all prerequisites for the NatLang Todo project are installed and working. Run this before scaffolding or when the learner first opens the project. Checks Rust, Node, pnpm, Tauri system deps, and Ollama.
---

You are running a preflight check to verify the learner's development environment is ready for this Tauri 2 + Svelte 5 + Rust project.

Run all checks silently, then present a single summary. Do NOT ask the learner to run commands themselves — just run them.

## Checks to Run

Run these commands via Bash and collect the results:

```bash
# Rust toolchain
rustc --version 2>&1
cargo --version 2>&1

# Node.js (need 18+)
node --version 2>&1

# pnpm
pnpm --version 2>&1

# Tauri system dependencies (macOS)
xcode-select -p 2>&1

# Ollama (needed for Phase 7+, but good to know now)
ollama --version 2>&1
```

## Report Format

Present results as a checklist:

```
## Preflight Check

- [x] Rust: 1.XX.X (via rustup)
- [x] Node.js: vXX.X.X (18+ required)
- [x] pnpm: XX.X.X
- [x] Xcode CLT: installed
- [ ] Ollama: not found — needed in Phase 7, install later from https://ollama.com

All clear — ready to scaffold!
```

## Handling Failures

For each missing tool, provide:
1. What it is (one sentence)
2. How to install it (the exact command)
3. Whether it blocks getting started or can be installed later

Specifically:
- **Rust missing**: Blocks everything. Install: `curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`
- **Node missing**: Blocks everything. Recommend `nvm install --lts` or `brew install node`
- **pnpm missing**: Blocks everything. Install: `npm install -g pnpm`
- **Xcode CLT missing** (macOS): Blocks Tauri compilation. Install: `xcode-select --install`
- **Ollama missing**: Does NOT block — not needed until Phase 7. Note it and move on.

## After the Check

If everything passes (or only Ollama is missing), tell the learner they're ready:

```
Ready to go! Run these to get started:

  pnpm install
  pnpm tauri dev

Then read docs/phases/phase-01.md for your first task — exploring the scaffold and understanding how the pieces connect.
```

If anything critical is missing, list exactly what to install and offer to re-check after they've done it.
