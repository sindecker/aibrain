# AIBrain - Self-Improving Agent Brain

> Your AI agent, better every day. Persistent memory, agent teams, flow orchestration, document ingestion.

AIBrain gives any AI agent persistent memory that improves over time. Install it once, and your agent remembers everything across sessions, learns from every conversation, and coordinates with other agents over encrypted mesh networking.

Works with **Claude Code, Cursor, Copilot, Ollama, llama.cpp** -- any agent that supports MCP or file-based configuration. Not locked to any provider.

## Install

```bash
pip install aibrain
aibrain setup
```

## What's New in v1.2.0

- **Agent Teams** -- Typed agents with role/goal/backstory, structured tasks with guardrails, sequential and hierarchical execution
- **Flow Engine** -- `@start`, `@listen`, `@router` decorators for state machines with conditional routing and human-in-the-loop
- **Document Ingestion** -- `brain.ingest("report.pdf")` with 7 source types and sentence-aware chunking
- **Universal MCP Client** -- Connect to ANY MCP server via Stdio, HTTP, or SSE
- **Tool Hooks** -- Intercept and modify tool/LLM calls
- **33 Event Types** -- Full lifecycle observability

## Quick Start

```python
from aibrain import Agent, Task, Team, Brain

researcher = Agent(role="Researcher", goal="Find accurate data")
writer = Agent(role="Writer", goal="Write compelling content")

research = Task(description="Research AI trends", expected_output="Summary report", agent=researcher)
article = Task(description="Write blog post", expected_output="1000 word post", agent=writer, context=[research])

team = Team(agents=[researcher, writer], tasks=[research, article])
result = team.kickoff()

brain = Brain()
brain.ingest("quarterly_report.pdf")
```

## Features

- **Persistent Memory** -- Semantic search with selective query routing (SelRoute)
- **Agent Teams** -- Compose agents into teams with guardrails, delegation, and memory injection
- **Flow Engine** -- Multi-step workflows with conditional branching and approval gates
- **Document Ingestion** -- PDF, CSV, Excel, JSON, Text, directories into searchable memory
- **Universal MCP Client** -- Connect to any MCP server, three transports, persistent registry
- **Self-Improvement** -- Evolution engine extracts learnings and consolidates memory
- **200+ Workflows** -- Email, code review, security, content, job tracking, and more
- **85 Skills** -- Pre-loaded capabilities
- **9 MCP Servers** -- 72 tools out of the box
- **Tool Hooks** -- @before/@after interceptors for logging, security, policy
- **45-Page Dashboard** -- Teams, flows, ingestion, MCP hub, knowledge graph, cost tracking
- **Tailscale Mesh** (Pro) -- Encrypted P2P agent coordination
- **Telegram + Email** -- Notifications and inbox automation

## Memory Routing (SelRoute)

Selective routing validated across 4 models, 4 benchmarks, 4,486 evaluations. [Read the paper](https://github.com/sindecker/selroute)

## Pricing

| | Free | Pro ($9.95/mo) | Team ($29.95/mo) |
|---|---|---|---|
| Memory + teams + flows + ingestion | Yes | Yes | Yes |
| 200+ workflows + 85 skills | Yes | Yes | Yes |
| 9 MCP servers + universal client | Yes | Yes | Yes |
| Dashboard + evolution + hooks | Yes | Yes | Yes |
| Tailscale mesh networking | - | Yes | Yes |
| Shared team brain | - | - | Yes |

## Links

- **Website:** [myaibrain.org](https://myaibrain.org)
- **PyPI:** [pypi.org/project/aibrain](https://pypi.org/project/aibrain/)
- **SelRoute Paper:** [github.com/sindecker/selroute](https://github.com/sindecker/selroute)

652 tests passing. Python 3.10+. Linux, macOS, Windows.

## License

Proprietary. Copyright (c) 2026 Matthew McKee. All rights reserved.
