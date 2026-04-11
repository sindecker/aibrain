# AIBrain -- Your AI agent that remembers, learns, and acts

> **Stop configuring. Start automating.** One install. 60 ready-to-run workflows. Agent teams. Flow engine. Document ingestion. Universal MCP. Self-improving via honest-rubric divergence detection. Runs locally, no cloud lock-in.

AIBrain is a self-hosted operating system for AI agents. It gives any agent persistent memory, typed Agent/Task/Team composition, a decorator-driven Flow engine, document ingestion, universal MCP client connectivity, a reactive workflow engine, a self-improvement loop with honest-rubric divergence detection, multi-model LLM routing, an approval queue, inter-agent messaging, and 60 ready-to-run workflows -- all behind a 53-page browser dashboard. Deploy it on a laptop, a VPS, or in Docker; your agent carries its entire brain with it.

![AIBrain](https://img.shields.io/badge/AIBrain-v1.4.1-00F5D4?style=flat-square) ![License](https://img.shields.io/badge/license-Proprietary-blue?style=flat-square) ![Python](https://img.shields.io/badge/python-3.10+-blue?style=flat-square) ![Tests](https://img.shields.io/badge/tests-2065-00F5D4?style=flat-square) ![Workflows](https://img.shields.io/badge/workflows-60-00F5D4?style=flat-square) ![Dashboard](https://img.shields.io/badge/dashboard-53_pages-61DAFB?style=flat-square) ![MCP](https://img.shields.io/badge/MCP-native-FFB000?style=flat-square)

---

## What's New in v1.4.1

Shipped 2026-04-12. Patch release — security hardening + Stripe per-user wiring + V2 persona registry.

- **AIBRAIN_SIGNING_KEY now mandatory** — `aibrain_licensing.py` no longer falls back to a hardcoded signing key. The env var must be set or import raises `ValueError` at import time. **A public hardcoded fallback would let anyone reading the open-source repo mint valid license keys.** Fail-loud at import is the right contract for paid software. After upgrading: `export AIBRAIN_SIGNING_KEY="<your secret>"` before running anything aibrain-related.
- **Per-user license_state writes** — `backend/main.py` now writes license state per user via `_write_license_state_per_user()`, with `client_reference_id → users.id → email → users.email` resolution. Closes the Stripe webhook attribution gap.
- **V2 persona registry wired** — `aibrain/core/workflow_runner.py` now registers `intel_agent_v2` and the rest of the V2 persona fleet. `aibrain/core/superworker.py` resolves `*_v2.md` persona paths before falling back to V1.
- **Content gate 14 (template_freshness)** — Pipeline now has 14 gates. Gate 14 reads the live truth doc and blocks publish on any stale version reference or unresolved template variable. 37/37 gate tests passing.
- **Defensive None handling** — `workflow_runner` print statements no longer crash when quality/model_used/duration_ms are None (which is the new contract per the honest-measurement rebuild — defaults are None until measured).
- **README template variables** — README now auto-renders to the current state via the live truth doc instead of being a manual sync surface.

## What's New in v1.4.0

Shipped 2026-04-11. The methodology layer.

- **Honest-rubric divergence loop** -- Every workflow execution produces both a self-score and an independent eval-score. When they diverge beyond a threshold, the run is flagged for review. The harness REFUSES to fake quality numbers — defaults are `None` until a real measurement lands. This is the load-bearing piece of the compounding learning loop.
- **Cost honesty refuse gate** -- The harness rejects dishonest `cost_cents=0` for non-deterministic tasks. If a run claims it cost nothing but used a paid model, it fails the gate. No more silently lying audit trails.
- **Workflow harness wire (P0)** -- Every workflow execution flows through `aibrain.core.workflow_runner` with task classification, model routing, authority check, quality eval, audit trail, and learning feedback. One pipeline, no bypass.
- **Hippocampus-as-a-service MCP endpoint** -- Standalone MCP server at `aibrain/mcp/server.py` (JSON-RPC 2.0 over stdio). Six tools: `memory_store`, `memory_search`, `memory_recall`, `memory_stats`, `memory_ingest`, `memory_prepare`. Bearer token auth. Stable v1.0.0 schema. Client recipes for Cursor, Claude Code, Zed, Continue.
- **Public/private lens split** -- 50-entry public floor (20 roles + 13 specialty + 10 archetypes + 2 always-on + 5 enterprise stubs). Tier-aware lens routing. CI-enforced wheel-leak check — no private personas ship in the public package.
- **Canonical-DB invariant (split-brain prevention)** -- 37-file route through one resolver. Pre-commit linter blocks any new direct DB path. `aibrain doctor` CLI for health checks. `secrets_filter` to prevent credential leaks in commits.
- **Token-to-cents equivalence** -- `aibrain usage` CLI for sub-allowance tracking. Cost honesty enforced at the harness level.
- **2,065 tests passing** -- Up from 869 in the v1.2.4 release. The honest-measurement rebuild caught five stale assertions in `test_harness.py` that were enforcing the old "fake-a-default" behavior — those were updated to match the contract.
- **53-page browser dashboard** -- Up from 45 pages.
- **Built-in benchmarks** -- 8 results captured with honest RECOR loss + +25.7 to +35.7 gains across LoCoMo and LongMemEval.

Earlier capability layers (still shipped):

- **Agent/Task/Team system** -- Typed multi-agent composition. Define agents with role, goal, and backstory. Create tasks with expected output and tools. Run teams with sequential or hierarchical processes, delegation, and guardrails with retry.
- **Flow engine** -- Decorator-driven state machines. `@start`, `@listen`, `@router` decorators for conditional routing. Typed state, persistence for long-running flows, human-in-the-loop pause/resume, `@retry`, `@timeout`, and visualization.
- **Universal MCP client** -- Connect to any MCP server from Python. Three transports (stdio, HTTP, SSE), tool bridging, and a registry for managing multiple servers.
- **Document ingestion** -- `brain.ingest("file.pdf")`. 7 source types (PDF, CSV, Excel, JSON, JSONL, Text, Markdown, directories). Sentence-aware chunking, incremental ingestion.
- **Tiered Retrieval** -- `smart_search` routes queries through category summaries, SelRoute reranking, memory deduplication, explainable recall, and salience scoring.
- **Tool Hooks** -- `@before` and `@after` interceptors for logging, security, and policy enforcement on any tool or LLM call.
- **Security hardening** -- Full audit. ShellTool allowlisted, PythonREPL sandboxed, SSRF protection, CSRF middleware, secrets vault hardened.

---

## Quick Start

```bash
python3 -m venv ~/aibrain-env && source ~/aibrain-env/bin/activate
pip install aibrain
aibrain setup    # interactive wizard — walks you through everything
aibrain serve    # dashboard at http://localhost:8001
```

The setup wizard handles config, API keys, database init, and workflow selection. No manual file editing needed.

**Alternative: Docker**
```bash
pip install aibrain && aibrain setup
cp config.json.example config.json   # edit with your API keys and preferences
docker compose up --build             # dashboard at http://localhost:5173
```

**Brain commands** -- export, import, and merge agent knowledge:
```bash
aibrain brain stats                    # show brain statistics
aibrain brain export                   # export brain to JSON
aibrain brain import brain.json        # import another brain's knowledge
aibrain brain merge /path/or/git-url   # merge from git repo or local path
```

For local development without Docker, see [Development Setup](#development-setup) below.

---

## Agent Teams

Define agents, give them tasks, run them as a team:

```python
from aibrain import Agent, Task, Team

researcher = Agent(
    role="Research Analyst",
    goal="Find accurate, current information",
    backstory="Senior analyst with 10 years of market research experience",
)

writer = Agent(
    role="Technical Writer",
    goal="Produce clear, actionable reports",
    backstory="Former journalist who translates complex topics into plain English",
)

research_task = Task(
    description="Research the current state of MCP adoption across AI tools",
    expected_output="A structured summary with key findings and trends",
    agent=researcher,
)

report_task = Task(
    description="Write a 500-word briefing from the research findings",
    expected_output="A concise report suitable for a technical audience",
    agent=writer,
)

team = Team(
    agents=[researcher, writer],
    tasks=[research_task, report_task],
    process="sequential",  # or "hierarchical"
)

result = team.kickoff()
print(result.raw)
```

---

## Flow Engine

Build state machines with decorators:

```python
from aibrain.core.flow import Flow, start, listen, router
from pydantic import BaseModel

class ReviewState(BaseModel):
    document: str = ""
    score: float = 0.0
    approved: bool = False

class ReviewFlow(Flow[ReviewState]):

    @start()
    def load_document(self):
        self.state.document = "quarterly report content..."
        return self.state.document

    @listen(load_document)
    def score_document(self):
        self.state.score = 0.85
        return self.state.score

    @router(score_document)
    def decide(self):
        if self.state.score >= 0.8:
            return "approve"
        return "reject"

    @listen("approve")
    def approve(self):
        self.state.approved = True
        return "Document approved"

    @listen("reject")
    def request_revision(self):
        return "Revision needed"

flow = ReviewFlow()
result = flow.kickoff()
```

---

## Document Ingestion

Feed documents directly into your brain:

```python
from aibrain import Brain

brain = Brain()

# Ingest a single file
brain.ingest("report.pdf")
brain.ingest("data.csv")
brain.ingest("notes.md")

# Ingest an entire directory
brain.ingest("/path/to/documents/")

# Search across ingested content
results = brain.search("quarterly revenue")
```

Supports PDF, CSV, Excel, JSON, JSONL, Text, Markdown, and directories. Sentence-aware chunking with configurable overlap. Incremental -- re-running skips already-processed files.

---

## Universal MCP Client

Connect to any MCP server and use its tools:

```python
from aibrain.mcp import MCPServerRegistry, MCPServerStdio, MCPServerHTTP

registry = MCPServerRegistry()

# Add a stdio-based MCP server
registry.register("github", MCPServerStdio(
    command="npx",
    args=["@github/mcp-server"],
))

# Add an HTTP-based MCP server
registry.register("custom", MCPServerHTTP(
    url="http://localhost:9000/mcp",
))

# List all registered servers and their tools
for name, server in registry.servers.items():
    print(f"{name}: {server}")
```

Three transports: stdio, HTTP, and SSE. Tool bridging lets you call remote MCP tools as local Python functions.

---

## Features

**Agent/Task/Team System** -- Typed multi-agent composition. Define agents with role, goal, and backstory. Create tasks with expected output, tools, and guardrails. Run teams with sequential or hierarchical processes. Agents can delegate to each other. Failed tasks retry automatically with configurable limits.

**Flow Engine** -- Decorator-driven state machines for complex workflows. `@start`, `@listen`, and `@router` decorators define execution graphs with conditional routing. Typed state objects via Pydantic. Persistence for long-running flows. Human-in-the-loop pause/resume. `@retry` and `@timeout` decorators. Flow visualization.

**Document Ingestion** -- `brain.ingest("file.pdf")` feeds documents directly into memory. 7 source types: PDF, CSV, Excel, JSON, JSONL, Text, Markdown, and full directories. Sentence-aware chunking with configurable overlap. Incremental ingestion skips already-processed files.

**Universal MCP Client** -- Connect to any MCP server from Python. `MCPServerRegistry` manages multiple servers. Three transports: stdio, HTTP, and SSE. Tool bridging calls remote MCP tools as local Python functions.

**53-Page Dashboard** -- Home, Memories, Knowledge Graph, Workflows, **Visual Workflow Builder**, **Content Pipeline**, Chat, Approvals, Agents, Activity Timeline, Library, Webhooks, Costs, MCP Hub, Skills Marketplace, Settings, Setup Wizard, Command Palette, Mobile Layout, Mobile Pairing, Toast notifications, Notification Center, Keyboard Shortcuts, System Logs, API Playground, Backup Manager, Task Runner, Integrations Hub, Onboarding Tour, Error Boundary, Teams, Flows, Ingest, Company Org Charts, Agent Detail, Goals, Secrets Vault, and more. Every aspect of your agent is visible and controllable from the browser.

**60 Ready-to-Run Workflows** -- Pre-built workflows across six registries (core, skill, user, content, ops, custom) covering productivity, research, communication, devops, self-improvement, code analysis, security, content generation, multi-agent ops, and business metrics. Every workflow execution flows through the universal harness with task classification, model routing, authority check, quality eval, audit trail, and learning feedback. Enable any workflow with a single toggle; schedules are fully configurable via cron expressions.

**Multi-Model LLM Router** -- Route tasks to local Ollama (free) or cloud Claude/GPT with automatic fallback via litellm. Track costs per model, per workflow, per day. Test provider connections directly from Settings.

**Natural Language Workflow Creation** -- Describe an automation in plain English ("Every morning check my email and summarize it") and AIBrain generates the workflow script and schedule.

**Visual Workflow Builder** -- Canvas-based drag-and-drop node editor. Four node types (trigger, action, condition, output) with 15 subtypes. Connect nodes with bezier curves, auto-layout with topological sort, save/load named workflows, export as JSON. Zero external dependencies -- pure canvas rendering.

**Knowledge Graph with Entity Extraction** -- Visualize memory connections as an interactive force-directed graph. Pattern-based NER automatically extracts technologies, URLs, emails, IPs, and proper names. Edges show references, shared tags, topic similarity, and entity connections with color-coded types and hover labels.

**Command Palette (Ctrl+K)** -- Global search across pages, actions, memories, workflows, and skills. Prefix with `>` for unified data search. Arrow-key navigation, instant routing.

**Real-time Notifications** -- WebSocket push for approval requests, activity events, and system alerts. Browser desktop notifications when approvals arrive. Live activity feed with auto-reconnect. Tab title shows pending approval count.

**Cost & Observability Dashboard** -- See exactly what your agent did, how much it cost, and where it failed. Trace every LLM call with token counts, latency, and cost. Set daily/monthly budgets with visual progress bars and overspend warnings.

**Mobile PWA** -- Install on your phone, pair via QR code, approve actions with one tap. Replaces Telegram as your mobile interface.

**SQLite + FTS5 Memory** -- Full-text searchable memory database with tagging, typing, and graph visualization. Export/import your agent's entire brain as JSON with one click from the dashboard.

**Skill Marketplace** -- Browse installed skills by category (Security, Development, Design, Automation, Media, Data, Communication). Install new skills from URL or pasted Markdown. Delete skills you no longer need. See which skills are connected to workflows and which are orphaned.

**Built-in Scheduler** -- APScheduler-backed cron engine reads `scheduled_jobs.json` on startup. Add, enable, disable, or trigger workflows from the UI or API. Run logs and next-fire times are always visible.

**Approval Queue** -- Workflows can request human approval before executing sensitive actions. Approve or reject from the dashboard or chat panel. WebSocket push keeps every client in sync with real-time desktop notifications.

**Real-time Chat** -- WebSocket chat panel with built-in commands (`/status`, `/approvals`, `/schedule`, `/costs`, `/content`, `/create`, `/help`). Messages are logged and accessible via REST for non-WebSocket clients.

**Deep Health Checks** -- `/health/deep` tests every subsystem: memory DB, scheduler, approvals, LLM providers, entity extractor, trace DB, and WebSocket connections. Results displayed in Settings.

**Content Pipeline** -- Full content management dashboard for creating, scheduling, and publishing across 7 platforms (LinkedIn, X/Twitter, Instagram, YouTube, Telegram, Email Newsletter, Blog). AI-powered content generation, queue and calendar views, platform-specific badges, and status tracking from draft through publication.

**Home Dashboard** -- Unified overview showing memory count, active workflows, pending approvals, LLM costs, recent activity, and upcoming scheduled jobs. Quick-action buttons navigate to any section.

**Multi-Agent Mesh** -- Broker integration and peer registry let multiple agents communicate. Register peers, send messages through the broker, and monitor connectivity from the Agents view.

**Webhook Event Triggers** -- Register incoming webhooks with optional HMAC signature validation. Webhooks trigger workflows on external events (GitHub pushes, payment notifications, CI results) as an alternative to polling on a schedule.

**System Logs** -- Terminal-style real-time log viewer with auto-scroll, pause/resume, level filtering (INFO/WARNING/ERROR/DEBUG), text search, and export to .txt.

**API Playground** -- Postman-style interactive endpoint tester with 17 pre-configured endpoints, query param inputs, JSON body editor, and response viewer with timing.

**Backup Manager** -- Create, restore, download, and delete memory snapshots. One-click brain export/import with file upload.

**Task Runner** -- Visual task queue with progress bars, duration tracking, cancel/retry actions, and live status updates.

**Integrations Hub** -- Central view of 12 services across 6 categories (Messaging, AI, Dev, Automation, Commerce, Finance) with connected/disconnected status and inline config.

**Onboarding Tour** -- 8-step guided walkthrough for first-time users with progress dots, skip option, and localStorage persistence.

**Keyboard Shortcuts** -- Press `?` for a full shortcut overlay. `N` toggles the notification center. `Ctrl+K` opens the command palette.

**Responsive Dashboard** -- The frontend works on any screen size. REST endpoints for chat and approvals work without WebSocket support.

**Reactive Workflow Engine** -- State-driven workflow execution inspired by the FlyWire fruit fly connectome and the Rete algorithm. Nodes fire when prerequisites are met, not in sequence. 180 lines, domain-agnostic, async-native. Forward chaining, refraction, priority-based conflict resolution, and SQLite-backed checkpoint recovery. Every workflow is a reactive graph, not a script.

**Evolution Engine** -- Self-improving automation. Outcomes feed patterns, patterns generate hypotheses, hypotheses run experiments, experiments get measured and kept or reverted. Self-criticism, loop detection, and positive pattern reinforcement. Your workflows get measurably better over time.

**Pattern Bus** -- Cross-domain event infrastructure. Every state change across every domain emits a DomainEvent to a SQLite-backed event table. Aggregator nodes detect anomalies and correlations. The foundation for self-initiated agent reasoning.

**Inter-Agent Messaging** -- Redis-backed pub/sub protocol for multi-agent coordination. Publish/subscribe events, request-response between agents, broadcast to all. Deploy one agent locally and another on a VPS -- they communicate through the broker.

**Durable Workflow Execution** -- Step-level retry with SQLite persistence. If a workflow fails mid-step, it resumes from the last successful step on restart. No external dependencies -- built on top of the reactive engine's checkpoint store.

**Docker Production Build** -- Multi-stage Dockerfile (Python 3.11 + Node 20) produces a single container running the API, scheduler, and nginx-served frontend with Redis broker for inter-agent messaging. Health checks and automatic restarts included.

**Cross-platform Launchers** -- `start.sh` (Unix/Mac), `start.bat` (Windows), and `Makefile` targets for dev, install, build, docker, and clean.

**Tool Hooks** -- `@before` and `@after` interceptors on any tool or LLM call. Log, filter, enforce policy, or transform inputs/outputs without touching the tool itself.

**33 Event Types** -- Full lifecycle observability. Agent, team, flow, tool, memory, and system events fire automatically. Subscribe to any event for custom reactions.

**Tiered Retrieval (smart_search)** -- Queries route through category summaries, SelRoute reranking, memory deduplication, explainable recall, and salience scoring. The best result path is chosen automatically.

**Experiment & Regression Testing** -- Built-in A/B framework for workflows, retrieval strategies, and agent configurations. Track metrics, compare variants, detect regressions.

**15 Built-in Tools** -- Ready-to-use tools for common agent tasks, available to any agent or workflow.

**8 LLM Adapters** -- Connect to Claude, GPT, Ollama, and more through a unified adapter interface.

**Security Hardened** -- Full audit with 24 findings fixed. ShellTool allowlisted, PythonREPL sandboxed, SSRF protection, CSRF middleware, secrets vault with Fernet encryption.

---

## Architecture

```
                          +------------------+
                          |     Browser      |
                          +--------+---------+
                                   |
                          HTTP / WebSocket
                                   |
                     +-------------+-------------+
                     |         nginx :5173        |
                     |   static frontend + proxy  |
                     +-------------+-------------+
                                   |
                            /api/* | /ws/*
                                   |
    +------------------------------+------------------------------+
    |                  FastAPI Backend :8001                       |
    |                                                             |
    |  +----------+  +-----------+  +-------+  +--------------+  |
    |  | Memory   |  | Reactive  |  | Chat  |  | Evolution    |  |
    |  | (SQLite  |  | Engine    |  | (WS)  |  | Engine       |  |
    |  |  + FTS5) |  | (Rete +   |  |       |  | (self-       |  |
    |  |          |  |  FlyWire) |  |       |  |  improving)  |  |
    |  +----+-----+  +-----+-----+  +---+---+  +------+-------+  |
    |       |              |             |             |           |
    |  +----+-----+  +----+------+  +---+---+  +------+-------+  |
    |  | Approval |  | Workflows |  | Agent |  | Pattern Bus  |  |
    |  | Queue    |  | (53)      |  | Broker|  | (events)     |  |
    |  +----------+  +-----------+  +---+---+  +--------------+  |
    +--------------------------------------------+-+--------------+
                |              |                 |
          aibrain.db    scheduled_jobs.json   Redis :6379
```

---

## Configuration

Copy `config.json.example` to `config.json` and fill in the values you need. Empty strings use sensible defaults. All fields are optional -- configure only what you use.

Three tiers of configuration (in priority order):

1. **Environment variables**: `AIBRAIN_MEMORY_DB`, `AIBRAIN_CRON_JOBS`, etc.
2. **config.json**: Copy `config.json.example` and customize
3. **Defaults**: Relative to the AIBrain root directory

### Key Fields

| Field | Description |
|---|---|
| `llm_provider` | LLM routing: `auto`, `claude`, `openai`, `ollama`, or `claude_cli` |
| `user_name` | Your name (shown in greeting and reports) |
| `budget_daily` | Daily LLM spending limit in USD (e.g., `5.00`) |
| `budget_monthly` | Monthly LLM spending limit in USD (e.g., `50.00`) |
| `agent_name` | Display name for your agent |
| `anthropic_api_key` | API key for Claude-powered workflows |
| `openai_api_key` | API key for OpenAI/GPT-powered workflows |
| `ollama_url` | Ollama API URL (default: `http://localhost:11434`) |
| `memory_db` | Path to SQLite memory database (default: `memory/memory.db`) |
| `cron_jobs` | Path to scheduled jobs registry (default: `scheduled_jobs.json`) |
| `skills_dir` | Directory containing agent skills |
| `email_accounts` | Array of email accounts to monitor (IMAP/SMTP config per account) |
| `email_reply_mode` | `"prompted"` (queue for approval) or `"auto"` |
| `email_auto_reply_senders` | Whitelist of senders for auto-reply mode |
| `location_name`, `location_lat`, `location_lon` | Location for weather workflows |
| `calendar_url`, `google_calendar_credentials` | Calendar integration |
| `github_token`, `github_username` | GitHub API access |
| `watched_repos`, `github_repos` | Repos to monitor for activity |
| `rss_feeds` | RSS/Atom feed URLs for the digest workflow |
| `arxiv_queries` | Search terms for the arXiv paper tracker |
| `job_keywords` | Keywords for job search workflow |
| `social_keywords` | Brand/topic terms for social listening |
| `price_watchlist` | Crypto/stock tickers with type, id, and display name |
| `habits` | Habits to track in the evening check-in |
| `scan_dirs` | Directories for file declutter workflow |
| `document_watch_dirs` | Directories for document parsing workflow |
| `uptime_targets` | URLs to monitor with expected HTTP status codes |

---

## Workflows

60 ready-to-run workflows span six registries plus standalone scripts in the `workflows/` directory. Enable any workflow with a single toggle in the dashboard or by setting `"enabled": true` in `scheduled_jobs.json`. Every execution flows through the universal harness — see "Honest-Rubric Harness" below.

### Registry Overview

| Registry | File | Focus |
|---|---|---|
| **Core** | `aibrain_workflows.py` | Productivity, research, communication, devops |
| **Skill** | `aibrain_skill_workflows.py` | Self-improvement, code review, security, intelligence |
| **User** | `aibrain_user_workflows.py` | Personal intelligence, career, knowledge, proactive alerts |
| **Content** | `aibrain_content_workflows.py` | Multi-platform content generation and scheduling |
| **Ops** | `aibrain_ops_workflows.py` | Multi-agent ops, business metrics, compliance |
| **Scripts** | `workflows/` directory | Standalone scripts (evolution, job search, memory, SEO) |

### Core Workflows

Productivity, research, communication, and developer tooling.

| Workflow | Category | Description |
|---|---|---|
| `daily_planner` | Productivity | Task plan from calendar, emails, and priorities |
| `habit_tracker` | Productivity | Evening habit check-in and streak tracking |
| `file_declutter` | Productivity | Clean up Downloads and Desktop folders |
| `rss_digest` | Research | Summarize new articles from RSS feeds |
| `arxiv_tracker` | Research | Track new papers matching your queries |
| `price_watcher` | Research | Crypto/stock price alerts on significant moves |
| `email_triage` | Communication | Check all email accounts, categorize messages |
| `email_auto_reply` | Communication | Draft and send replies (prompted or auto mode) |
| `github_digest` | Developer | Summarize GitHub activity across your repos |
| `uptime_monitor` | Developer | Check service availability, alert on downtime |

### Skill Workflows

Agent self-improvement, code analysis, security assessment, and research automation.

| Workflow | Category | Description |
|---|---|---|
| `skill_sharpener` | Self-Improvement | Identify and practice weak skill areas |
| `knowledge_gap_detector` | Self-Improvement | Find gaps in the agent's knowledge base |
| `code_review_runner` | Code & Architecture | Automated code review with actionable feedback |
| `tech_debt_analyzer` | Code & Architecture | Track and prioritize technical debt |
| `security_assessment_runner` | Security | Run security assessments against targets |
| `threat_model_generator` | Security | Generate threat models for systems |
| `autonomous_researcher` | Intelligence | Deep-dive research on any topic |
| `trend_detector` | Intelligence | Detect emerging trends from data sources |

### User Workflows

Personal intelligence, professional development, knowledge synthesis, and proactive alerts.

| Workflow | Category | Description |
|---|---|---|
| `decision_journal` | Personal | Track decisions and outcomes over time |
| `idea_incubator` | Personal | Develop and refine ideas with structured prompts |
| `resume_updater` | Professional | Auto-update resume from recent achievements |
| `skill_gap_analyzer` | Professional | Identify skills to develop for career goals |
| `cross_domain` | Knowledge | Find connections across different knowledge domains |
| `mental_models` | Knowledge | Build and apply mental models to problems |
| `opportunity_detector` | Proactive | Surface opportunities from memory patterns |
| `burnout_detector` | Proactive | Early warning signs from activity patterns |

### Content Workflows

Multi-platform content generation and performance tracking.

| Workflow | Category | Description |
|---|---|---|
| `linkedin_content` | Platform | Generate LinkedIn posts from memory and context |
| `x_twitter_content` | Platform | Generate X/Twitter threads and posts |
| `youtube_content` | Platform | Generate YouTube scripts and descriptions |
| `content_batch_scheduler` | Orchestration | Schedule content across all platforms weekly |
| `content_performance` | Orchestration | Analyze content metrics and optimize strategy |

### Ops Workflows

Multi-agent coordination, operational health, and business metrics.

| Workflow | Category | Description |
|---|---|---|
| `brain_sync_checker` | Multi-Agent | Verify brain consistency across agents |
| `task_handoff_monitor` | Multi-Agent | Track task handoffs between agents |
| `workflow_health` | Ops | Monitor workflow execution health and failures |
| `session_handover` | Ops | Generate session handover reports |
| `credential_rotation` | Security | Remind when credentials need rotation |
| `package_downloads` | Business | Track package download metrics |
| `launch_readiness` | Business | Pre-launch checklist validation |

### Custom Workflows

User-defined workflows for domain-specific automation. Create your own workflow files in the `workflows/` directory. Examples include agent mesh operations, security scanning, analytics, and business tracking.

| Workflow | Category | Description |
|---|---|---|
| `agent_sync_checker` | Agent Mesh | Verify sync state across all agents |
| `broker_health` | Agent Mesh | Monitor inter-agent broker connectivity |
| `bt_comparator` | Security | Compare scan results across runs |
| `finding_trends` | Security | Analyze vulnerability finding trends |
| `custom_metrics` | Analytics | Track custom metrics and KPIs |
| `data_reconciler` | Analytics | Reconcile data across sources |
| `pypi_tracker` | Business | Track PyPI package downloads |
| `ship_readiness` | Business | Product ship-readiness checklist |

Run `aibrain workflows` to see the complete list with schedules and status.

All core workflows use **free APIs only**: Open-Meteo, CoinGecko, ArXiv, Hacker News, Reddit JSON. No paid API keys required for basic operation.

---

## API Endpoints

The backend exposes a REST API at `http://localhost:8001`. All resource endpoints are prefixed with `/api/agent` unless noted otherwise.

### Memory

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/agent/memories` | List memories (paginated) |
| GET | `/api/agent/memories/search?q=` | Full-text search across all memories |
| GET | `/api/agent/memories/summary` | Memory count and type breakdown |
| GET | `/api/agent/memories/graph` | Graph nodes and edges for visualization |
| GET | `/api/agent/memories/export` | Export entire brain as JSON |
| POST | `/api/agent/memories/import` | Import brain from JSON |
| GET | `/api/agent/memories/{id}` | Get a single memory |
| POST | `/api/agent/memories` | Create a new memory |
| PUT | `/api/agent/memories/{id}` | Update a memory |
| DELETE | `/api/agent/memories/{id}` | Delete a memory |
| POST | `/api/agent/memories/extract-entities` | Backfill entity extraction on all memories |

### Workflows and Scheduler

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/agent/crons` | List all scheduled jobs |
| PUT | `/api/agent/crons/{name}` | Update a job's schedule or config |
| GET | `/api/agent/crons/upcoming` | Next-fire times for all jobs |
| GET | `/api/agent/crons/groups` | Jobs grouped by category |
| GET | `/api/agent/workflows` | List all available workflows |
| GET | `/api/agent/workflows/{name}` | Get workflow details and source |
| POST | `/api/agent/workflows/{name}/enable` | Enable a workflow |
| POST | `/api/agent/workflows/{name}/disable` | Disable a workflow |
| POST | `/api/agent/workflows/{name}/run` | Trigger a workflow immediately |
| GET | `/api/agent/scheduler/status` | Scheduler health and job count |
| POST | `/api/agent/scheduler/reload` | Reload all jobs from disk |

### Approval Queue

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/agent/approvals` | List items (filter by status/category) |
| GET | `/api/agent/approvals/summary` | Pending count and category breakdown |
| GET | `/api/agent/approvals/{id}` | Get a single approval item |
| POST | `/api/agent/approvals` | Create an approval request |
| POST | `/api/agent/approvals/{id}/approve` | Approve an item |
| POST | `/api/agent/approvals/{id}/reject` | Reject an item |

### Chat

| Method | Endpoint | Description |
|---|---|---|
| WebSocket | `/ws/chat` | Real-time chat with history replay on connect |
| GET | `/api/agent/chat/history` | Recent chat messages (REST fallback) |
| POST | `/api/agent/chat/send` | Send a chat message (REST fallback) |

### Agent Mesh

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/agent/status` | Agent status and system info |
| GET | `/api/agent/activity` | Activity timeline |
| POST | `/api/agent/activity` | Log an activity event |
| GET | `/api/agent/peers` | List registered peer agents |
| POST | `/api/agent/peers` | Register a new peer |
| DELETE | `/api/agent/peers/{name}` | Remove a peer |
| POST | `/api/agent/broker/send` | Send a message through the broker |
| GET | `/api/agent/broker/recv` | Receive messages from the broker |
| GET | `/api/agent/broker/status` | Broker connectivity status |

### Webhooks

| Method | Endpoint | Description |
|---|---|---|
| GET | `/webhooks` | List registered webhooks |
| POST | `/webhooks` | Register a new webhook |
| PUT | `/webhooks/{name}` | Update a webhook configuration |
| DELETE | `/webhooks/{name}` | Remove a webhook |
| POST | `/webhooks/{name}` | Invoke a webhook (external callers) |
| GET | `/webhooks/{name}/history` | Invocation history for a webhook |

### Search

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/agent/search?q=` | Unified search across memories, workflows, and skills |

### Skills

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/agent/skills` | List installed agent skills |
| GET | `/api/agent/skills/{name}` | Get skill details |
| POST | `/api/agent/skills/install` | Install a skill from URL or pasted content |
| DELETE | `/api/agent/skills/{name}` | Remove an installed skill |

### Configuration

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/agent/config` | Get current configuration |
| PUT | `/api/agent/config` | Update configuration |
| POST | `/api/agent/config/test-provider` | Test LLM provider connectivity |

### Tasks

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/agent/tasks` | List all tasks |
| POST | `/api/agent/tasks` | Create a new task |
| POST | `/api/agent/tasks/{id}/cancel` | Cancel a running task |
| POST | `/api/agent/tasks/{id}/retry` | Retry a failed task |

### Backups

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/agent/backups` | List all backups |
| POST | `/api/agent/backups` | Create a new backup |
| POST | `/api/agent/backups/{id}/restore` | Restore from a backup |
| GET | `/api/agent/backups/{id}/download` | Download a backup file |
| DELETE | `/api/agent/backups/{id}` | Delete a backup |

### Logs and Costs

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/agent/logs` | Structured log viewer |
| GET | `/api/agent/costs/summary` | LLM cost summary |
| GET | `/api/agent/costs/history` | Daily cost breakdown |

### System

| Method | Endpoint | Description |
|---|---|---|
| GET | `/health` | Health check with scheduler and approval status |
| GET | `/health/deep` | Deep health check -- tests all subsystems |
| WebSocket | `/ws/notifications` | Real-time push notifications for approvals and activity |

---

## Docker Deployment

The included `docker-compose.yml` runs the full stack: app server, frontend, and Redis broker.

```bash
# Copy and configure environment
cp env.example .env   # edit with your API keys

# Build and start
docker compose up --build -d

# View logs
docker compose logs -f aibrain

# Stop
docker compose down
```

The stack exposes:

- **5173** -- Dashboard UI (nginx serving the built React app)
- **8001** -- Backend API (FastAPI + uvicorn)
- **6379** -- Redis broker (inter-agent messaging)

Volumes persist your data across restarts:

- `./data` -- SQLite databases (aibrain.db)
- `./memory` -- Legacy memory database
- `./config.json` -- Your configuration
- `./workflows` -- Workflow scripts

For VPS-only deployment (no frontend, agent-to-agent only):

```bash
docker compose -f docker-compose.vps.yml up -d
```

The Dockerfile uses a multi-stage build: Python 3.11 for the backend, Node 20 for the frontend build, and a final slim image with nginx for production serving. Health checks run every 30 seconds against the `/api/health` endpoint.

---

## Boss Agent Orchestrator (Pro/Team)

Run multiple AI agent workers in parallel, each in its own isolated Docker container, coordinated by a single boss process on the host. Workers execute tasks independently -- shell commands, Python scripts, or full Playwright browser automation -- and merge their results back into one shared brain. More workers means faster knowledge accumulation.

```
Boss (host) ──→ Redis ──→ Worker 1 (Docker + Playwright)
                      ──→ Worker 2 (Docker + Playwright)
                      ──→ Worker N
              ←── Session merge ←── All workers feed one brain
```

### Quick Start

```bash
docker compose -f docker-compose.boss.yml up -d --scale worker=2
```

### CLI

```bash
python -m boss.boss assign "Research competitor pricing"   # assign a task
python -m boss.boss status                                 # check worker status
python -m boss.boss results                                # collect completed results
python -m boss.boss scale 4                                # scale to 4 workers
python -m boss.boss teardown                               # stop all workers
```

### Worker Types

| Type | Description |
|---|---|
| **Shell** | Execute shell commands in an isolated container |
| **Python** | Run Python scripts with full library access |
| **Playwright** | Browser automation -- login flows, scraping, form filling, screenshots |

### Features

- **Communication** -- Workers report progress, ask questions, and broadcast to peers through Redis channels
- **Budget guards** -- Token limits and iteration caps prevent runaway costs per task
- **Session merge** -- Results from all workers deduplicate and merge into the host brain, ranked by score
- **Evolution bridge** -- Worker outcomes feed the Evolution Engine's skill learning loop
- **Checkpoint and replay** -- Tasks checkpoint progress; a crashed worker resumes from its last checkpoint
- **Model routing** -- Complex reasoning routes to Opus, standard tasks to Sonnet, monitoring to Haiku

### Pricing

| Tier | Workers | Scope |
|---|---|---|
| **Free** | 1 instance | Single machine |
| **Pro** | Up to 3 workers | Single machine |
| **Team** | Up to 10 workers | Cross-machine coordination |

---

## MCP Server -- Connect Any AI Agent

AIBrain includes an MCP (Model Context Protocol) server that gives any compatible AI agent persistent memory with selective routing. Connect Claude Code, Cursor, Windsurf, or any MCP client.

**Performance:** #1 on LongMemEval benchmark (Ra@5=0.789, NDCG@5=0.796) -- beats Contriever (110M params) and Stella V5 (1.5B params) with a 22MB model + rule-based routing.

### Setup

Add to your `.claude.json` or `.mcp.json`:

```json
{
  "mcpServers": {
    "aibrain-memory": {
      "command": "python",
      "args": ["/path/to/aibrain/mcp_server.py"]
    }
  }
}
```

### Three Modes

| Mode | Config | What Ships | Performance |
|------|--------|-----------|-------------|
| **No ML** | `AIBRAIN_EMBEDDING_MODEL=none` | FTS5 only, 0 deps | NDCG 0.692 |
| **Default** | (no config needed) | MiniLM 22MB, 384-dim | Ra@5 0.789, NDCG 0.796 |
| **bge-base** | `AIBRAIN_EMBEDDING_MODEL=BAAI/bge-base-en-v1.5` | 110MB, 768-dim | Ra@5 0.791, NDCG 0.812 |
| **Custom** | `AIBRAIN_EMBEDDING_MODEL=your/model` | Any sentence-transformer | Routing adapts |

### Tools Provided

- `memory_store` -- Store a memory (auto-enriched for better retrieval)
- `memory_search` -- Search with selective routing (auto-detects query type, or pass `search_type` hint)
- `memory_recall` -- Recall top memories by importance

### Dependencies

```bash
pip install mcp sentence-transformers sqlite-vec
```

All optional -- the server gracefully degrades. No embeddings? FTS5 only. No enricher? Raw content. No sqlite-vec? Skip vector search.

---

## Development Setup

The fastest way to start both backend and frontend:

```bash
# Windows
start.bat

# Unix/Mac
./start.sh

# Or using Make
make dev
```

To run manually without the launchers:

```bash
# Backend
cd backend
pip install -r requirements.txt
python -m uvicorn main:app --host 0.0.0.0 --port 8001 --reload

# Frontend (separate terminal)
cd frontend
npm install
npm run dev
```

Other Make targets: `make install`, `make build`, `make docker`, `make clean`.

The frontend dev server runs on port 5173 and proxies API requests to port 8001.

---

## Connect Your Agent

AIBrain works with any AI agent -- Claude, GPT, Gemini, Ollama, or your own framework.

### Option 1: Just tell your agent

Paste this to any AI agent (Claude Code, ChatGPT, etc.):

```
You have access to AIBrain at http://localhost:8001
Store memories: POST /api/agent/memories  Body: {"name": "...", "content": "...", "type": "reference", "tags": ""}
Search memories: GET /api/agent/memories/search?q=your+query
Send chat: POST /api/agent/chat/send  Body: {"content": "message", "role": "agent"}
Dashboard: http://localhost:5173
```

### Option 2: Python SDK

```python
from aibrain_sdk import AIBrain

agent = AIBrain("http://localhost:8001")
agent.remember("user prefers dark mode", tags=["preference"])
results = agent.recall("dark mode")
agent.say("Task complete.")
approval = agent.request_approval("Deploy to production")
```

### Option 3: Example agents

```bash
# Claude
python examples/claude_agent.py

# OpenAI GPT
python examples/openai_agent.py

# Local LLM (Ollama -- no API key needed)
python examples/ollama_agent.py
```

The SDK uses only Python stdlib (urllib) -- zero dependencies. See `aibrain_sdk.py` for the full API.

---

## Remote Access

Open `http://<your-machine-ip>:5173` from any browser on your network. The responsive design adapts to any screen size. For remote access over the internet, use an SSH tunnel or put behind a reverse proxy with auth.

---

## Brain Export and Import

Transfer your agent's entire memory to another machine:

```bash
# Export
curl http://localhost:8001/api/agent/memories/export > brain.json

# Import on new machine
curl -X POST http://localhost:8001/api/agent/memories/import \
  -H "Content-Type: application/json" \
  -d @brain.json
```

Duplicates are automatically skipped during import.

---

## Project Structure

```
aibrain/
├── aibrain_sdk.py            # Python SDK — connect any agent
├── setup_wizard.py           # Interactive first-run setup
├── docker-compose.yml        # One-command Docker deploy
├── Dockerfile                # Multi-stage production build
├── Makefile                  # dev, install, build, docker, clean
├── start.sh                  # Unix/Mac launcher
├── start.bat                 # Windows launcher
├── config.json.example       # Configuration template
├── scheduled_jobs.json       # Workflow schedules
├── backend/
│   ├── main.py               # FastAPI server + scheduler + chat + WS
│   ├── aibrain_api.py        # Memory, cron, workflow, skill, search APIs
│   ├── chat_responder.py     # Multi-provider LLM chat (Claude/GPT/Ollama/CLI)
│   ├── llm_router.py         # litellm-backed unified model routing
│   ├── entity_extractor.py   # Pattern-based NER for knowledge graph
│   ├── trace_logger.py       # LLM call tracing with cost tracking
│   ├── action_executor.py    # Approved action execution engine
│   ├── workflow_generator.py # Natural language to workflow script
│   ├── webhooks.py           # Webhook registration and invocation
│   ├── scheduler.py          # APScheduler cron engine
│   ├── approval_queue.py     # Human-in-the-loop approval system
│   └── requirements.txt      # Python dependencies
├── frontend/
│   ├── src/App.jsx           # Router + 28-component navigation
│   └── src/components/
│       ├── HomeDashboard.jsx      # System overview with sparklines
│       ├── MemoryDashboard.jsx    # Memory search, browse, edit
│       ├── MemoryGraph.jsx        # Force-directed knowledge graph
│       ├── CronDashboard.jsx      # Workflow scheduling and control
│       ├── ChatPanel.jsx          # WebSocket chat with commands
│       ├── ApprovalQueue.jsx      # Human-in-the-loop approvals
│       ├── AgentStatus.jsx        # Agent health and peer mesh
│       ├── ActivityTimeline.jsx   # Real-time activity feed (WS)
│       ├── WorkflowBuilder.jsx    # Visual drag-and-drop workflow editor
│       ├── WorkflowLibrary.jsx    # Content pipeline and library
│       ├── WebhooksDashboard.jsx  # Webhook management
│       ├── CostDashboard.jsx      # LLM cost tracking and budgets
│       ├── MCPHub.jsx             # MCP server management
│       ├── SkillMarketplace.jsx   # Skill browse, install, delete
│       ├── Settings.jsx           # Config, provider testing, health
│       ├── SetupWizard.jsx        # First-run guided setup
│       ├── CommandPalette.jsx     # Global Ctrl+K search overlay
│       ├── NotificationCenter.jsx # Real-time notification panel
│       ├── KeyboardShortcuts.jsx  # Shortcut overlay (press ?)
│       ├── SystemLogs.jsx         # Terminal-style log viewer
│       ├── ApiPlayground.jsx      # Interactive API tester
│       ├── BackupManager.jsx      # Memory backup/restore
│       ├── TaskRunner.jsx         # Task queue with progress
│       ├── Integrations.jsx       # Service connection hub
│       ├── OnboardingTour.jsx     # First-run walkthrough
│       ├── ErrorBoundary.jsx      # React error boundary
│       ├── MobileLayout.jsx       # Mobile-optimized layout
│       ├── MobilePairing.jsx      # QR code pairing for mobile
│       └── Toast.jsx              # Toast notification component
├── examples/
│   ├── basic_agent.py        # Minimal agent example
│   ├── claude_agent.py       # Claude-powered agent
│   ├── openai_agent.py       # GPT-powered agent
│   └── ollama_agent.py       # Local LLM agent (no API key)
├── workflows/                # Self-contained workflow scripts
└── memory/
    └── memory.db             # SQLite + FTS5 database
```

---

## Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl+K` | Open command palette |
| `?` | Show keyboard shortcuts overlay |
| `N` | Toggle notification center |
| `>query` | Search memories, workflows, and skills from palette |
| `Escape` | Close any modal or palette |
| `Arrow keys` | Navigate command palette results |
| `Enter` | Select highlighted palette result |

---

## License

Proprietary. All rights reserved. See [LICENSE](LICENSE) for details.

For support, visit [myaibrain.org](https://myaibrain.org).
