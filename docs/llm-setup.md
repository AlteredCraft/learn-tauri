# LLM Setup Guide

This app uses a local LLM (via Ollama) by default. The LLM's job is narrow: classify the user's natural language into a structured intent (add, complete, delete, list, etc.). A small model works well for this.

## Option 1: Ollama (Recommended)

### Install Ollama

**macOS:**
```bash
brew install ollama
```

Or download from [ollama.com](https://ollama.com).

**Linux:**
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

### Pull a model

```bash
# Recommended: small and fast, good enough for intent classification
ollama pull llama3.2:3b

# Alternative: slightly better quality
ollama pull qwen2.5:3b
```

### Start Ollama

Ollama runs as a background service. After installation:

```bash
# macOS: starts automatically, or run:
ollama serve

# Verify it's running:
curl http://localhost:11434/api/tags
```

You should see a JSON response listing your models.

### Configure the app

The default `.env` works with Ollama out of the box:

```
LLM_PROVIDER=ollama
LLM_BASE_URL=http://localhost:11434
LLM_MODEL=llama3.2:3b
```

## Option 2: OpenAI-compatible API

Any provider that implements the `/v1/chat/completions` endpoint works. This includes OpenAI, Groq, Together AI, and local servers like vLLM.

### Configure

Edit `.env`:

```
LLM_PROVIDER=openai-compatible
LLM_BASE_URL=https://api.openai.com/v1
LLM_API_KEY=sk-your-key-here
LLM_MODEL=gpt-4o-mini
```

### Examples for other providers

**Groq:**
```
LLM_BASE_URL=https://api.groq.com/openai/v1
LLM_API_KEY=gsk_your-key-here
LLM_MODEL=llama-3.1-8b-instant
```

**Together AI:**
```
LLM_BASE_URL=https://api.together.xyz/v1
LLM_API_KEY=your-key-here
LLM_MODEL=meta-llama/Llama-3.2-3B-Instruct-Turbo
```

## Troubleshooting

**"Connection refused" when the app tries to reach the LLM:**
- Is Ollama running? Try `ollama serve` or check if the service is active
- Is the URL correct in `.env`? Default is `http://localhost:11434`

**LLM returns garbage / can't classify intents:**
- Try a larger model: `ollama pull llama3.1:8b`
- Check that the system prompt is being sent correctly (Phase 8)
- Some models handle structured JSON output better than others

**Slow responses:**
- Local LLM speed depends on your hardware. A 3B model should respond in 1-3 seconds on Apple Silicon
- For faster responses, use a cloud API (Groq is particularly fast)
