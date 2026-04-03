# JARVIS OMEGA

Distributed Multi-AI Operating System — 961 agents, 6 GPUs, zero cloud dependency.

## Architecture

- **OpenClaw** : 42 agents, qwen3.5-9b primary, Telegram integration
- **Claude Code** : 57 agents, 10 MCP servers, auto-bypass permissions
- **Domino Engine** : 74 auto-triggered chains with timeout protection
- **Lumen** : Multi-IA hub (STT/TTS/LLM routing) on port 8788
- **Cluster** : M1 (6 GPU), M2 (deepseek-r1), OL1 (Ollama), M3 (fallback)

## Stack

| Component | Port | Model |
|-----------|------|-------|
| LM Studio M1 | 1234 | qwen3.5-9b |
| Ollama OL1 | 11434 | qwen2.5:1.5b |
| LM Studio M2 | 192.168.1.26:1234 | deepseek-r1 |
| OpenClaw Gateway | 18789 | Multi-model routing |
| Lumen Token Server | 8788 | Multi-IA hub |
| Redis | 6379 | State store (128K keys) |
| Domino Engine | 9750 | Chain executor |

## Web AI Integration (CDP)

- Perplexity (research)
- ChatGPT (generation)
- Claude.ai (reasoning)
- Gemini AI Studio (vision)

## Author

**Turbo** (Franc Delmas) — Independent AI Systems Engineer
