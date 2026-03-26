---
name: verify
description: Run project checks — clippy, tests, and frontend validation. Use after making changes to verify nothing is broken.
---

Run the following verification steps for the natlang-todo project. Report results clearly, stopping at the first failure category.

## Rust Backend

```bash
cd src-tauri
cargo clippy -- -D warnings 2>&1
cargo test 2>&1
```

## Frontend

```bash
pnpm check 2>&1
```

## Report

- If everything passes: confirm with a one-line summary
- If anything fails: show the failure output and suggest a fix
- If a command doesn't exist yet (e.g., no tests written): skip it and note why
