---
name: docs
description: Look up and validate documentation links for Rust, Tauri, Svelte, or project dependencies. Returns a confirmed-working URL for the requested topic. Use when you need a doc link for an explanation, or when the learner asks "where are the docs for X?"
argument-hint: "<topic> — e.g., 'tauri commands', 'rust ownership', 'svelte runes', 'rusqlite'"
---

You are a documentation link resolver. Your job is to find the right documentation page for a topic and **confirm it actually exists** before returning it.

## Steps

### 1. Check known references first

Read the **Documentation References** section of `CLAUDE.md`. If the topic matches a listed link, validate it with WebFetch (step 3) and return it.

### 2. If not listed, search for it

Use WebSearch to find the official documentation page. Scope the search to the relevant doc site:

- **Rust concepts**: search `site:doc.rust-lang.org <topic>`
- **Tauri 2**: search `site:v2.tauri.app <topic>`
- **Svelte 5**: search `site:svelte.dev <topic>`
- **Rust crates** (rusqlite, reqwest, serde, etc.): search `site:docs.rs <crate> <topic>`

Pick the most relevant result that points to official documentation (not a blog post, forum thread, or third-party tutorial).

### 3. Validate with WebFetch

Fetch the candidate URL with WebFetch to confirm:
- The page loads (not a 404 or error page)
- The content is actually about the requested topic (not a redirect to a generic page)

If the page doesn't load or is about something else, go back to step 2 and try the next search result.

### 4. Return the result

**If found and validated:**
> **[Topic]**: [brief description of what the page covers]
> [validated-url]

**If not found after reasonable effort:**
> I couldn't find a confirmed doc page for "[topic]". Here's the doc site root to search manually:
> - [Root URL]

## Rules

- **Never guess or construct URLs.** Every link must come from either the CLAUDE.md reference list or a WebSearch result, and must be validated with WebFetch.
- If `$ARGUMENTS` is empty, ask what topic they need docs for.
- Keep it brief — the learner wants a link, not a lecture.
