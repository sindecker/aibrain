# Changelog

All notable changes to AIBrain will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.4.1] - 2026-04-12

### Security
- **AIBRAIN_SIGNING_KEY now mandatory (P0)** — `aibrain_licensing.py` no longer falls back to a hardcoded signing key. The env var must be set or import raises `ValueError` at import time. A public hardcoded fallback would let anyone reading the open-source repo mint valid license keys. Fail-loud at import is the correct contract. Upgraders must `export AIBRAIN_SIGNING_KEY="<secret>"` before any aibrain command works.

### Added
- **Per-user license_state writes** — `backend/main.py` now writes license state per user via the new `_write_license_state_per_user()` helper. `client_reference_id → users.id → email → users.email` resolution chain so the Stripe webhook knows which user to credit. Closes the multi-user attribution gap from the LeadEng Stripe fix bundle.
- **V2 persona registry** — `aibrain/core/workflow_runner.py` registers `intel_agent_v2` and the rest of the V2 persona fleet in `WORKFLOW_REGISTRY`. New `update_aibrain_live_truth` workflow registered.
- **V2 persona path resolution** — `aibrain/core/superworker.py` looks for `*_v2.md` in `decker_system/02_subagents/` before falling back to V1, honoring the V2-preference rule across the spawn path.
- **Content gate 14 (template_freshness)** — Pipeline now has 14 gates. Gate 14 reads the live truth doc and blocks publish on any stale version reference or unresolved template variable. 37/37 gate tests passing.
- **Schema additions for `license_state` table** — `aibrain_db.py` adds the per-user license_state schema + connection-pool fixes.

### Improved
- **README template variables** — README now auto-renders to the current state via the live truth doc instead of being a manual sync surface.

### Fixed
- **Defensive None handling in `workflow_runner`** — Print statements no longer crash when `quality`/`model_used`/`duration_ms` are `None` (which is the new contract per the honest-measurement rebuild — defaults are None until a real measurement lands). Pattern #11 fix.

## [1.4.0] - 2026-04-11

### Added
- **Honest-rubric divergence loop** — Every workflow execution produces both a self-score and an independent eval-score. When they diverge beyond a threshold, the run is flagged for review. The harness REFUSES to fake quality numbers — defaults are `None` until a real measurement lands. Load-bearing piece of the compounding learning loop.
- **Cost honesty refuse gate** — The harness rejects dishonest `cost_cents=0` for non-deterministic tasks. If a run claims it cost nothing but used a paid model, it fails the gate. No silently lying audit trails.
- **Workflow harness wire (P0)** — Every workflow execution flows through `aibrain.core.workflow_runner` with task classification, model routing, authority check, quality eval, audit trail, and learning feedback. One pipeline, no bypass.
- **Hippocampus-as-a-service MCP endpoint** — Standalone MCP server at `aibrain/mcp/server.py` (JSON-RPC 2.0 over stdio). Six tools: `memory_store`, `memory_search`, `memory_recall`, `memory_stats`, `memory_ingest`, `memory_prepare`. Bearer token auth. Stable v1.0.0 schema. Client recipes for Cursor, Claude Code, Zed, Continue.
- **Public/private lens split** — 50-entry public floor (20 roles + 13 specialty + 10 archetypes + 2 always-on + 5 enterprise stubs). Tier-aware lens routing via `lens_router.py`. CI-enforced wheel-leak check — no private personas ship in the public package.
- **Canonical-DB invariant (split-brain prevention)** — 37-file route through one resolver. Pre-commit linter blocks any new direct DB path. `aibrain doctor` CLI for health checks. `secrets_filter` to prevent credential leaks in commits.
- **Token-to-cents equivalence** — `aibrain usage` CLI for sub-allowance tracking. Cost honesty enforced at the harness level.
- **Built-in benchmarks** — 8 benchmark results captured with honest RECOR loss + +25.7 to +35.7 gains across LoCoMo and LongMemEval.

### Improved
- **2,065 tests passing** — Up from 869 in v1.2.4. Five stale `test_harness.py` assertions updated to match the honest-measurement contract (defaults are `None`, not `0.0`).
- **53 dashboard pages** — Up from 45 pages.
- **Pricing rate table corrections** — Opus 4.6 $5/$25, Haiku 4.5 $1/$5, Sonnet 4.x $3/$15. Bare family model enrichment ("sonnet" → "sonnet-unknown") so the harness never misroutes to a stale model name.

### Fixed
- **`_find_db` existence check** (Bug #6) — read/write semantics split, fresh installs no longer silently no-op.
- **Test suite repair** — pre-existing failures and errors resolved across `test_harness.py`, `test_brain_export_import.py`, `test_category_summaries.py`.
- **`__version__` runtime string** — bumped to 1.4.0 (caught by fresh-venv smoke test).
- **Google app password regex** — tightened to require an explicit label, eliminates the "floor with zero pers" false positive.

## [1.2.4] - 2026-04-07

### Improved
- **Content audit** -- Fixed 51 findings across all public-facing content. Corrected tool counts (15 built-in tools), removed internal product names, removed competitor names, marked Brain Marketplace as conceptual, replaced dead links, standardized version and test counts.
- **Test suite** -- 869 tests passing across all modules.

## [1.2.3] - 2026-04-07

### Added
- **Pre-publish scanner** -- Automated content scanner that catches internal names, stale stats, and dead links before any public release.

### Improved
- **Dashboard** -- Full QA audit fixes across 10 files, 21 issues resolved.

## [1.2.2] - 2026-04-07

### Security
- **Full security audit** — 24 findings identified and fixed across the entire codebase.
- **ShellTool allowlisted** — Shell tool execution restricted to approved commands only.
- **PythonREPL sandboxed** — Python REPL tool runs in a restricted sandbox environment.
- **SSRF protection** — Server-side request forgery guards on all outbound HTTP calls.
- **CSRF middleware** — Cross-site request forgery protection on all state-changing API endpoints.
- **Secrets vault hardened** — Fernet encryption strengthened with additional access controls.

### Added
- **Salience scoring** — Memories scored by contextual relevance for smarter recall.

### Improved
- **Test suite** — 847 tests passing across all modules (now 869 in v1.2.4).

## [1.2.1] - 2026-04-07

### Added
- **Tool Hooks** — `@before` and `@after` interceptors on any tool or LLM call for logging, security, and policy enforcement.
- **33 Event Types** — Full lifecycle observability across agent, team, flow, tool, and memory operations.
- **Tiered Retrieval (smart_search)** — Queries route through category summaries, SelRoute reranking, memory deduplication, explainable recall, and salience scoring.
- **Category Summaries** — Automatic summarization of memory categories for faster high-level retrieval.
- **SelRoute Reranker** — Integrated SelRoute paper methodology into the retrieval pipeline for hybrid ranking.
- **Memory Deduplication** — Automatic detection and merging of duplicate or near-duplicate memories.
- **Explainable Recall** — Retrieval results include explanations of why each memory was returned.
- **Experiment & Regression Testing** — Built-in A/B framework for workflows, retrieval strategies, and agent configurations.

### Improved
- **Architecture unification** — Consolidated retrieval, event, and hook systems into a coherent pipeline.

## [1.2.0] - 2026-04-07

### Added
- **Agent/Task/Team system** — Typed composition for multi-agent orchestration. `from aibrain import Agent, Task, Team`. Define agents with role/goal/backstory, create tasks with expected output and tools, run teams with sequential or hierarchical processes. Supports delegation between agents and guardrails with automatic retry.
- **Flow engine** — Decorator-driven state machines for complex workflows. `from aibrain.core.flow import Flow, start, listen, router`. Typed state objects, conditional routing with `@router`, persistence for long-running flows, human-in-the-loop pause/resume, `@retry` and `@timeout` decorators, and flow visualization.
- **Universal MCP client** — Connect to any MCP server from Python. `from aibrain.mcp import MCPServerRegistry, MCPServerStdio, MCPServerHTTP`. Three transports (stdio, HTTP, SSE), tool bridging to use remote MCP tools as local functions, and a registry for managing multiple servers.
- **Document ingestion** — Feed documents directly into brain memory. `brain.ingest("file.pdf")`. Supports 7 source types: PDF, CSV, Excel, JSON, JSONL, Text, Markdown, and full directories. Sentence-aware chunking with configurable overlap. Incremental ingestion skips already-processed files.
- **Teams in Companies** — Create teams from company agents and run them with task assignments. View team results from the dashboard.
- **4 new dashboard pages** — /teams (team management and execution), /flows (flow visualization and state), /ingest (document ingestion UI), /mcp (updated MCP server registry and tool browser).

### Improved
- **Dashboard** — Now 45+ pages with team, flow, ingestion, and MCP management views.
- **Test suite** — 617 tests passing across all modules.

## [1.1.6] - 2026-04-06

### Added
- **Companies feature** — Create AI agent teams with CEO delegation, heartbeat scheduling, and LLM-powered task execution. Agents wake on schedule, claim tasks, produce output, and store learnings. CEO agents automatically delegate to idle workers.
  - `aibrain company create/list/status`
  - `aibrain agent hire/list/heartbeat`
  - `aibrain task create/list`
  - `aibrain inbox/approve/reject`
- **Smart archive system** — Files are archived (not deleted) with 30-day hold. Tracks access to archived items and resets timer when referenced. Prevents accidental data loss from agent actions.
- **Companies tier limits** — Free: 1 company + 2 agents. Pro: unlimited + 10. Pioneer pricing active.

### Improved
- **CEO delegation** — When CEO has no tasks, it uses LLM to create and assign tasks to idle workers based on company mission.
- **Heartbeat picks up in_progress tasks** — Prevents stuck tasks from interrupted heartbeats.
- **Hire approval processing** — CEO automatically creates agents when board approves hire requests.
- **Config sync** — config.json edits now picked up on every init (bidirectional sync with DB).

## [1.1.5] - 2026-04-06

### Added
- Companies database tables (5 tables + 7 indexes)
- Heartbeat scheduler for company agents

## [1.1.4] - 2026-04-06

### Security
- **Secrets encryption** — Sensitive config keys (API keys, tokens, passwords) now transparently encrypted with Fernet (AES-256) on write and decrypted on read. 8 sensitive keys protected.

### Added
- **Budget enforcement** — `budget_policies` and `budget_incidents` tables. Per-global/agent/workflow budget caps with 80% warning and 100% hard stop thresholds. Default: $50/month global.
- **CLI observability** — `aibrain audit` shows execution history with quality scores, models, costs. `aibrain health` shows brain overview with tier, success rate, resource usage bars.
- **Paperclip memory skill** — AIBrain installable as a Paperclip skill via GitHub URL. Gives Paperclip agents persistent memory across heartbeats.
- **Harness execution rule** — All scheduled workflows now route through the execution harness automatically.

### Improved  
- **Evaluator** — Deterministic tasks with None output now score 0.7 ("nothing to do" = success, not failure).
- **Scheduled jobs** — Updated to use harnessed runner for eval/audit on every automated execution.

## [1.1.3] - 2026-04-06

### Added
- **Model routing with local Ollama** — Simple tasks route to local models (qwen2.5, gemma3) for $0 execution. Deterministic tasks skip LLM entirely. Falls back to cloud APIs only when needed.
- **Workflow runner** — All 63 workflows registered with task type, authority level, and budget caps. Universal `harnessed_runner.py` for scheduled jobs.
- **Memory retention policy** — Auto-archive stale memories by type (completions after 30 days, projects after 90 days). User/feedback/pattern memories kept forever.
- **Config revision audit trail** — Every config change logged with before/after values for rollback capability.
- **Authority enforcement** — 22 permissions in database with allow/deny/require_approval. Force push explicitly denied.

### Improved
- **Quality evaluator** — Now handles all Python return types (dict, tuple, bool, list, str) with type-aware scoring.
- **Ollama fallback routing** — Uses routing table models instead of hardcoded defaults. Tries each model in chain until one responds.

## [1.1.2] - 2026-04-06

### Security
- **Tier limit enforcement** — Free tier limits now properly enforced on workflow activation (max 10) and satellite DB creation (max 3). Previously limits existed in code but were never checked at execution time.
- **SQL injection hardening** — 6 functions now use `frozenset` column whitelists. No f-string SQL patterns remain in mutation functions.

### Added
- **Execution Harness** (`aibrain.core.harness`) — Universal wrapper for all tasks. Every action passes through: classify → budget check → authority check → model route → execute → evaluate quality (0.0-1.0) → audit trail → learn. Four task types route to appropriate models: deterministic ($0), simple (Haiku), judgment (Sonnet), complex (Opus).
- **Workflow Runner** (`aibrain.core.workflow_runner`) — Routes all workflows through the harness with per-workflow capability scoping (task type, authority level, budget cap).
- **Execution Audit Trail** — New `execution_log` table records every harnessed execution with actor, action, quality score, model used, cost, duration, and before/after state.
- **Download Tracker** — Daily PyPI download stats added to morning digest.
- **Competitive Analysis Skill** — Reusable 7-phase methodology for studying and learning from competitor products.

### Architecture
- **4 authority levels** — read_only, standard, elevated, admin. Actions gated by authority level.
- **4 task types with model routing** — deterministic (code only, $0), simple (Haiku/local), judgment (Sonnet), complex (Opus). Saves tokens on tasks that don't need expensive models.
- **Quality scoring** — Every task output scored 0.0-1.0. Below-threshold results trigger retry or escalation.

## [1.1.0] - 2026-04-04

### Added

- **Boss Agent Orchestrator** — coordinate multiple worker instances from a single boss agent.
  - Workers execute tasks in isolated Docker containers with their own Playwright browsers.
  - Redis-based task queuing and result collection.
  - Session merge with content-hash deduplication — no duplicate memories across workers.
  - Mid-task communication: progress updates, clarification questions, and broadcast messages.
  - Budget guards with token and iteration limits plus a dead-letter queue for failed tasks.
  - Evolution bridge — worker execution outcomes feed back into the skill learning engine.
  - Checkpoint/replay for crash recovery so no work is lost on container restart.
- **SKILL_TEMPLATE.md** — master template for developing new skills with standardized structure.
- **Model tier routing** — skills declare their required model tier (opus/sonnet/haiku/local) and the router selects the appropriate model automatically.

### Changed

- **40 skills rewritten** to the Docker worker harness standard. Every skill now includes: identity/scope declaration, model tier requirement, entry/exit criteria, structured JSON output, escalation triggers, rationalizations to reject, and success criteria.

### Dependencies

- New optional dependency group `boss` (`redis>=5.0.0`, `docker>=7.0.0`). Install with `pip install aibrain[boss]`.
- Boss module (`boss/`) now included in package distribution.

## [1.0.1] - 2026-04-04

### Fixed

- Windows unicode crash — set PYTHONUTF8 on import to prevent UnicodeEncodeError with non-ASCII memory content.
- `importance` keyword argument now accepted correctly by memory_store.
- Backend version string corrected.

## [0.9.0] - 2026-03-27

### Added

- **MCP Server** with 3 tools (memory_store, memory_search, memory_recall) and selective query routing. #1 on LongMemEval benchmark (Ra@5=0.789, NDCG@5=0.796).
- **Reactive Workflow Engine** inspired by FlyWire connectome and Rete algorithm. State-driven, async-native, domain-agnostic. Forward chaining with priority-based conflict resolution and SQLite checkpoint recovery.
- **41 Automated Workflows** across 10 categories (productivity, research, finance, job hunting, social, comms, devops, health, intelligence, system). All use free APIs only.
- **Evolution Engine** for self-improving workflows. Outcomes feed patterns, patterns generate hypotheses, hypotheses run experiments, results are measured and kept or reverted.
- **Pattern Bus** for cross-domain event infrastructure with SQLite-backed event table and anomaly detection.
- **Task Router** with self-expanding routes and ABM simulation runner.
- **Visual Workflow Builder** with canvas-based drag-and-drop node editor, 4 node types, 15 subtypes, bezier curves, auto-layout.
- **Knowledge Graph** with pattern-based NER, force-directed visualization, entity extraction for technologies, URLs, emails, IPs.
- **Content Pipeline** for creating, scheduling, and publishing across 7 platforms (LinkedIn, X, Instagram, YouTube, Telegram, Email Newsletter, Blog).
- **30 Dashboard Components** including Home, Memories, Knowledge Graph, Workflows, Chat, Approvals, Agents, Activity Timeline, Settings, and more.
- **Multi-Model LLM Router** via litellm with automatic fallback (Ollama/Claude/GPT). Per-model, per-workflow, per-day cost tracking.
- **Natural Language Workflow Creation** — describe an automation in plain English and AIBrain generates the script and schedule.
- **Command Palette** (Ctrl+K) with global search across pages, actions, memories, workflows, and skills.
- **Real-time Notifications** via WebSocket push with browser desktop notifications and live activity feed.
- **Mobile PWA** with QR code pairing and one-tap approvals.
- **SQLite + FTS5 Memory** with full-text search, tagging, typing, and one-click brain export/import.
- **Skill Marketplace** for browsing, installing, and managing agent skills by category.
- **APScheduler Cron Engine** with add/enable/disable/trigger from UI or API.
- **Approval Queue** with WebSocket push sync and real-time desktop notifications.
- **Multi-Agent Mesh** with broker integration and peer registry for inter-agent communication.
- **Webhook Event Triggers** with optional HMAC signature validation.
- **Docker Production Build** with multi-stage Dockerfile (Python 3.11 + Node 20), nginx, Redis, health checks.
- **Security hardening** — SSRF protection, rate limiting, API key auth, JWT auth, request ID tracing, input sanitization.
- **165+ API routes** covering memory, workflows, scheduler, approvals, chat, agent mesh, webhooks, search, skills, tasks, backups, logs, costs, and health.
- **Cross-platform launchers** — start.sh (Unix/Mac), start.bat (Windows), Makefile targets.
- **Python SDK** (zero dependencies, stdlib only) with remember/recall/say/request_approval.
- **CI/CD pipeline** with GitHub Actions (lint, smoke tests, Docker build on release tags).
- **Feature tier system** skeleton (free/pro) for future gating.

[1.2.2]: https://pypi.org/project/aibrain/1.2.2/
[1.2.1]: https://pypi.org/project/aibrain/1.2.1/
[1.2.0]: https://pypi.org/project/aibrain/1.2.0/
[1.1.0]: https://pypi.org/project/aibrain/1.1.0/
[1.0.1]: https://pypi.org/project/aibrain/1.0.1/
[0.9.0]: https://pypi.org/project/aibrain/0.9.0/
