# AIBrain - Self-Improving Agent Brain

> Your AI agent, better every day. Persistent memory, selective routing, multi-agent mesh.

AIBrain gives any AI agent persistent memory that improves over time. Install it once, and your agent remembers everything across sessions, learns from every conversation, and coordinates with other agents over encrypted mesh networking.

Works with **Claude Code, Cursor, Copilot, Ollama, llama.cpp** -- any agent that supports MCP or file-based configuration. Not locked to any provider.

## Install

```bash
pip install aibrain
aibrain setup
```

That's it. The setup wizard handles config, API keys, database, hooks, and MCP server registration.

## What You Get

- **Persistent Memory** -- Semantic search across all previous sessions. Your agent remembers what worked, what didn't, and what you prefer.
- **Self-Improvement** -- Evolution cycles extract learnings, detect patterns, and consolidate memory between sessions. Your agent genuinely gets better over time.
- **200+ Workflows** -- Email triage, code review, security scanning, content scheduling, job tracking, and more. Enable with a toggle.
- **85 Skills** -- Pre-loaded capabilities your agent can use immediately. Add your own.
- **9 MCP Servers** -- Memory, peer discovery, web search, IT support, calendar, design, desktop automation, file management.
- **Tailscale Mesh Networking** (Pro) -- Agents on different machines share memory, transfer files, and coordinate work over encrypted P2P connections.
- **Telegram + Email Integration** -- Your agent messages you when tasks finish, checks your inbox, and responds automatically.
- **Dashboard** -- 30-component browser UI with visual workflow builder, knowledge graph, cost tracking, and mobile PWA.
- **Brain Marketplace** (Coming) -- Buy and sell domain-specific trained brains.
- **Setup Wizard** -- One command configures everything. Works on Linux, macOS, and Windows.

## Memory Routing (SelRoute)

AIBrain uses **SelRoute** -- a selective routing approach for memory retrieval validated across 4 models, 4 benchmarks, and 4,486 evaluations. Different query types get routed to different retrieval strategies instead of one-size-fits-all vector similarity.

[Read the paper](https://github.com/sindecker/selroute)

## Pricing

| | Free | Pro ($9.95/mo) | Team ($29.95/mo) |
|---|---|---|---|
| Persistent memory | Yes | Yes | Yes |
| 200+ workflows + 85 skills | Yes | Yes | Yes |
| 9 MCP servers | Yes | Yes | Yes |
| Evolution cycles | Yes | Yes | Yes |
| Dashboard + PWA | Yes | Yes | Yes |
| Tailscale mesh networking | - | Yes | Yes |
| Brain marketplace | - | Yes | Yes |
| Shared team brain | - | - | Yes |
| Up to 10 agents | - | - | Yes |

## Documentation

- [Getting Started](https://myaibrain.org/docs/getting-started.html)
- [User Guide](https://myaibrain.org/docs/guide.html)
- [Workflow Reference](https://myaibrain.org/docs/workflows.html)

## Links

- **Website:** [myaibrain.org](https://myaibrain.org)
- **PyPI:** [pypi.org/project/aibrain](https://pypi.org/project/aibrain/)
- **SelRoute Paper:** [github.com/sindecker/selroute](https://github.com/sindecker/selroute)

## Architecture

```
Your Agent (Claude, Cursor, Copilot, Ollama, any MCP client)
    |
    v
AIBrain (brain layer)
    |
    +-- Memory DB (SQLite + embeddings + SelRoute)
    +-- 200+ Workflows (cron-scheduled, toggleable)
    +-- 9 MCP Servers (72 tools)
    +-- Tailscale Mesh (Pro -- encrypted P2P)
    +-- Dashboard (30 components, PWA)
    +-- Evolution Engine (self-improvement)
    +-- Telegram + Email (notifications, inbox)
```

## Requirements

- Python 3.10+
- Node.js 18+ (for dashboard, optional)
- Works on Linux, macOS, Windows

## License

Proprietary. Copyright (c) 2026 Matthew McKee. All rights reserved.
