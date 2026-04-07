# AIBrain — The Complete Guidebook

**Version:** 1.2.2 | **Last updated:** 2026-04-07

AIBrain is a portable agent operating system that gives any AI agent persistent memory, typed Agent/Task/Team composition, a decorator-driven Flow engine, document ingestion, universal MCP client connectivity, self-learning workflows, multi-agent coordination, and a full browser dashboard. It works with Claude, GPT, Ollama, or any LLM — no vendor lock-in.

This guidebook covers everything AIBrain can do. Use it as a reference when setting up, a manual when building, or a briefing document when onboarding a new agent.

---

## Table of Contents

1. [Quick Start](#1-quick-start)
2. [Core Concepts](#2-core-concepts)
3. [Memory System](#3-memory-system)
4. [CLI Reference](#4-cli-reference)
5. [Workflows](#5-workflows)
6. [Brain Packs](#6-brain-packs)
7. [Dashboard & UI](#7-dashboard--ui)
8. [API Reference](#8-api-reference)
9. [MCP Servers](#9-mcp-servers)
14b. [Universal MCP Client](#14b-universal-mcp-client)
10. [Networking & Tailscale Mesh](#10-networking--tailscale-mesh)
11. [Multi-Agent Mesh](#11-multi-agent-mesh)
11b. [Boss Agent Orchestrator](#11b-boss-agent-orchestrator-proteam)
12. [Agent/Task/Team System](#12-agenttaskteam-system)
13. [Flow Engine](#13-flow-engine)
14. [Document Ingestion](#14-document-ingestion)
15. [Stack System](#15-stack-system)
16. [Self-Learning & Evolution](#16-self-learning--evolution)
17. [Skill Evolution](#17-skill-evolution)
18. [Auto-Experiment](#18-auto-experiment)
19. [IT Support](#19-it-support)
20. [Risk Analyst](#20-risk-analyst)
21. [Tier System & Pricing](#21-tier-system--pricing)
22. [Mobile (PWA)](#22-mobile-pwa)
23. [Installation & Updates](#23-installation--updates)
24. [Deployment](#24-deployment)
25. [Configuration](#25-configuration)
26. [Troubleshooting](#26-troubleshooting)

---

## 1. Quick Start

```bash
# Install
git clone https://github.com/sindecker/aibrain.git
cd aibrain
python install.py

# Start
aibrain start              # Restore stack + launch services
aibrain status             # See what's running

# Core operations
aibrain search "query"     # Search your brain
aibrain summary            # Dashboard stats
aibrain workflows list     # See all workflows
aibrain packs              # Browse brain packs
aibrain server             # Launch dashboard at :8001
```

If you have an existing database from another system (brain.db, memory.db, agent.db, or any SQLite with a `memories` table), AIBrain auto-detects and migrates it on install.

---

## 2. Core Concepts

### The Brain
Your brain is a SQLite database (`aibrain.db`) containing everything the agent knows: memories, skills, workflows, configuration, activity logs, and relationship graphs. It's portable — copy the file to move your entire brain to another machine.

### Memories
Structured knowledge units with types (user, feedback, project, reference, pattern, decision), importance scores, tags, timestamps, and optional vector embeddings for semantic search.

### Workflows
Automated tasks that run on schedules (cron) or triggers. AIBrain ships with 55+ workflow scripts covering productivity, research, communication, security, self-learning, and more. Each workflow reads from and writes to the brain.

### Brain Packs
Domain specialization bundles. Activating a pack enables a curated set of workflows for a specific use case (developer, content creator, security pro, etc.).

### Stack
The complete running state of your agent: which workflows are scheduled, which hooks are active, which services are running. Stacks can be saved, restored, and shared.

### Agents, Tasks, and Teams
Typed multi-agent composition. Define agents with role/goal/backstory, create tasks with expected output and tools, and run them as teams with sequential or hierarchical processes.

### Flows
Decorator-driven state machines. Build complex workflows with `@start`, `@listen`, and `@router` decorators. Typed state, conditional routing, persistence, and human-in-the-loop support.

### Document Ingestion
Feed documents directly into memory. `brain.ingest("file.pdf")` handles PDF, CSV, Excel, JSON, JSONL, Text, Markdown, and directories with sentence-aware chunking.

### Mesh
Multi-agent coordination. Multiple AIBrain instances can sync memories, resolve conflicts by consensus, and distribute merged knowledge back to all agents.

---

## 3. Memory System

### Storage
- **SQLite + FTS5** — Full-text search across all memories, instant results
- **Vector embeddings** (optional) — Semantic search using MiniLM (22MB, default) or bge-base (110MB)
- **Hyperedge relationships** — Multi-node connections, not just pairwise links
- **Entity extraction** — Auto-detects technologies, URLs, emails, IPs, names

### Memory Types
| Type | Purpose | Example |
|------|---------|---------|
| `user` | Who the user is, preferences, expertise | "Senior Go developer, new to React" |
| `feedback` | Corrections and validated approaches | "Never mock the database in integration tests" |
| `project` | Ongoing work, goals, deadlines | "Merge freeze begins April 5 for mobile release" |
| `reference` | External resources and locations | "Pipeline bugs tracked in Linear project INGEST" |
| `pattern` | Recurring systemic tendencies | "Verify before declaring done" |
| `decision` | Architectural and strategic choices | "Chose PostgREST over FastAPI for auto REST" |

### Search Modes
AIBrain uses **selective query routing** — it auto-detects your query type and routes to the best search strategy:

- **Keyword queries** → FTS5 full-text search (fast, exact matching)
- **Semantic queries** → Vector similarity search (meaning-based)
- **Hybrid queries** → Both, with result fusion

This is based on the SelRoute research paper (benchmarked across 4 models, 4 benchmarks, 4,486 evaluations).

### Operations
```bash
aibrain search "kubernetes deployment"     # Search memories
aibrain summary                            # Memory stats
aibrain brain export                       # Export as JSON
aibrain brain import backup.json           # Import from file
aibrain brain merge https://github.com/... # Merge from remote
aibrain forget "outdated topic"            # Preview deletion
aibrain forget "outdated topic" --confirm  # Permanently delete
```

### MCP Tools (for AI agents)
```
memory_store   — Save a memory (auto-enriched for retrieval)
memory_search  — Search with selective routing
memory_recall  — Load top memories by importance
```

---

## 4. CLI Reference

### Setup & System
| Command | Description |
|---------|-------------|
| `aibrain setup` | Interactive first-time setup wizard |
| `aibrain init` | Initialize database |
| `aibrain update` | Safe self-update (backup, pull, validate) |
| `aibrain version` | Print version |
| `aibrain start` | Restore stack + start services |
| `aibrain status` | Show current stack state |
| `aibrain server` | Start dashboard at :8001 |
| `aibrain mcp` | Start MCP server (stdio) |

### Memory Management
| Command | Description |
|---------|-------------|
| `aibrain summary` | Dashboard statistics |
| `aibrain search <query>` | Cross-table FTS search |
| `aibrain browse` | Open memory browser in web browser |
| `aibrain forget <topic>` | Preview what would be deleted |
| `aibrain forget <topic> --confirm` | Permanently delete |

### Brain Operations
| Command | Description |
|---------|-------------|
| `aibrain brain stats` | Brain statistics |
| `aibrain brain export` | Export brain to JSON |
| `aibrain brain import <file>` | Import from JSON |
| `aibrain brain merge <source>` | Merge from git URL or path |

### Workflow Management
| Command | Description |
|---------|-------------|
| `aibrain workflows list` | List all workflows + status |
| `aibrain workflows enable <name>` | Enable workflow (auto-enables deps) |
| `aibrain workflows disable <name>` | Disable workflow |
| `aibrain workflows enable --recommended` | Enable starter set |
| `aibrain workflows sync` | Create OS scheduled tasks |
| `aibrain workflows sync --dry-run` | Preview changes |
| `aibrain workflows run <name>` | Run immediately |
| `aibrain workflows deps` | Show dependency map |

### Brain Packs
| Command | Description |
|---------|-------------|
| `aibrain packs` | Browse available packs |
| `aibrain packs activate <name>` | Activate a pack |
| `aibrain packs active` | Show active packs |

### Stack Management
| Command | Description |
|---------|-------------|
| `aibrain stack save [name]` | Save named snapshot |
| `aibrain stack restore [name]` | Restore from snapshot |
| `aibrain stack rollback` | Roll back to previous |
| `aibrain stack set-default` | Save current as default |
| `aibrain stack list` | List saved snapshots |

### Multi-Agent Mesh
| Command | Description |
|---------|-------------|
| `aibrain mesh status` | Show mesh agent status |
| `aibrain mesh register <name> <db>` | Register agent |
| `aibrain mesh merge` | Merge all agents (consensus) |
| `aibrain mesh distribute` | Push merged brain to all |
| `aibrain mesh swarm create <type> -n 100` | Create training swarm |
| `aibrain mesh swarm harvest <type>` | Merge swarm results |

### Brain Versioning
| Command | Description |
|---------|-------------|
| `aibrain history snapshot <msg>` | Snapshot current state |
| `aibrain history list` | List all snapshots |
| `aibrain history rollback <id>` | Roll back to snapshot |
| `aibrain history branch <name>` | Branch for experiments |
| `aibrain history diff <a> [b]` | Compare versions |

### Brain Scaling
| Command | Description |
|---------|-------------|
| `aibrain scale stats` | Brain health report |
| `aibrain scale compact` | Archive stale memories |
| `aibrain scale analyze` | Shard recommendations |

### Marketplace
| Command | Description |
|---------|-------------|
| `aibrain marketplace package --name <n>` | Package brain for sale |
| `aibrain marketplace validate <file>` | Validate .brain file |
| `aibrain marketplace publish <file>` | Publish to marketplace |
| `aibrain marketplace search <query>` | Search marketplace |
| `aibrain marketplace install <id>` | Install a brain |
| `aibrain marketplace revenue` | Revenue dashboard |

### ATT&CK Framework
| Command | Description |
|---------|-------------|
| `aibrain attack build` | Download ATT&CK STIX + build graph |
| `aibrain attack search <query>` | Search techniques, malware, tools |
| `aibrain attack technique <id>` | Full technique info |
| `aibrain attack mitigations <id>` | Mitigations for technique |
| `aibrain attack tactic <name>` | Techniques by tactic |
| `aibrain attack chain <id1> <id2>` | Find attack paths |
| `aibrain attack stats` | Graph statistics |

### Configuration
| Command | Description |
|---------|-------------|
| `aibrain config get <key>` | Read config value |
| `aibrain config set <key> <value>` | Write config value |
| `aibrain license` | Show license status |
| `aibrain license activate <key>` | Activate license key |
| `aibrain license tiers` | Show pricing tiers |

---

## 5. Workflows

AIBrain ships with 55+ workflow scripts. Each runs independently and reads/writes to the brain database.

### Productivity
| Workflow | Schedule | What it does |
|----------|----------|-------------|
| `daily_planner` | 7am daily | Task plan from calendar, emails, priorities |
| `habit_tracker` | 9pm daily | Evening habit check-in and streak tracking |
| `file_declutter` | Weekly | Clean up Downloads and Desktop |
| `calendar_manager` | Hourly | Calendar sync and management |
| `bookmark_organizer` | Weekly | Organize and categorize bookmarks |
| `email_to_task` | On trigger | Convert emails to actionable tasks |
| `meeting_notes` | On trigger | Capture and summarize meeting notes |
| `smart_file_organizer` | Weekly | Intelligent file organization by type/project |

### Research & Intelligence
| Workflow | Schedule | What it does |
|----------|----------|-------------|
| `arxiv_tracker` | Daily | Track new papers matching your interests |
| `rss_digest` | Daily | Summarize articles from RSS feeds |
| `daily_research` | Daily | Research summaries from multiple sources |
| `price_watcher` | Hourly | Crypto/stock price alerts on significant moves |
| `security_advisories` | Daily | CISA KEV, NVD, GitHub advisory monitoring |
| `newsletter_digest` | Daily | Email newsletter aggregation |
| `social_listener` | Daily | Brand/topic monitoring from social media |
| `weather_briefing` | Daily | Weather briefing for your location |

### Communication
| Workflow | Schedule | What it does |
|----------|----------|-------------|
| `email_triage` | 2x daily | Check all accounts, categorize messages |
| `email_auto_reply` | On trigger | Draft and send replies |
| `telegram_check` | Every 10m | Telegram message monitoring |
| `morning_check_email_summarize` | 7am | Morning email summary |
| `report_generator` | On demand | Generate reports on any topic |

### Developer & DevOps
| Workflow | Schedule | What it does |
|----------|----------|-------------|
| `github_digest` | Daily | Summarize GitHub activity across repos |
| `git_monitor` | Every 4h | Watch for repo changes |
| `uptime_monitor` | Every 30m | Service availability monitoring |
| `dependency_auditor` | Weekly | Audit dependencies for CVEs |
| `service_monitor` | Every 2h | Monitor service health |
| `heartbeat` | Every 30m | System health check (disk, memory, services) |

### Self-Learning
| Workflow | Schedule | What it does |
|----------|----------|-------------|
| `memory_consolidation` | Weekly | Merge redundant memories into principles |
| `contradiction_detector` | Weekly | Find and resolve conflicting memories |
| `learning_extractor` | Every 4h | Extract learnings from experiences |
| `memory_md_sync` | Every 6h | Sync MEMORY.md with live DB |
| `evolution_experiment` | 2x/week | Run self-improvement experiments |
| `evolution_auto_loop` | Every 12h | Full improvement cycle: insights -> hypotheses -> experiments |
| `knowledge_graph_builder` | Every 6h | Build entity/relationship graph from memories |
| `cross_domain_questions` | Weekly | Find connections across different domains |
| `risk_analyst` | Daily 8pm | Retrospective risk scan of recent actions |

### System
| Workflow | Schedule | What it does |
|----------|----------|-------------|
| `db_backup` | 2x daily | Database backup to timestamped copy |
| `weekly_report` | Friday 6pm | Full weekly review |
| `cron_health` | Daily | Monitor workflow execution health |
| `session_continuity` | Every 2h | Verify key memories survived compaction |

### Job Search & Career
| Workflow | Schedule | What it does |
|----------|----------|-------------|
| `job_search_apply` | Weekdays | Automated job search and application |
| `interview_prep` | On demand | Interview preparation with STAR stories |
| `job_response_checker` | Daily | Monitor responses to applications |

---

## 6. Brain Packs

Brain packs are domain specialization bundles. Activating a pack enables a curated set of workflows.

### Free Tier
| Pack | Workflows | Description |
|------|-----------|-------------|
| **Productivity** | 8 | Email management, daily planning, habits, file organization, calendar |

### Pro Tier ($9.95/mo)
| Pack | Workflows | Description |
|------|-----------|-------------|
| **Developer** | 7 | Code review, architecture evaluation, dependency audit, security scanning |
| **Content Creator** | 10 | Multi-platform content (LinkedIn, X, Reddit, Medium, TikTok, Instagram, YouTube) |
| **Business** | 7 | Revenue tracking, competitor monitoring, analytics, ship readiness |
| **Security Pro** | 6 | Security assessments, threat modelling, CVE tracking |
| **Research** | 5 | ArXiv, RSS, cross-domain insights, knowledge graph |
| **Multi-Agent Ops** | 6 | Agent sync, memory diff, handover generation, cross-agent evolution |
| **Job Hunter** | 5 | Automated search, application tracking, interview prep |

```bash
aibrain packs                      # Browse all packs
aibrain packs activate developer   # Activate developer pack
aibrain packs active               # See what's active
```

---

## 7. Dashboard & UI

Launch with `aibrain server` — opens at `http://localhost:8001`.

### Pages
| Page | What it does |
|------|-------------|
| **Home** | System overview: memory count, active workflows, pending approvals, costs, recent activity |
| **Memories** | Search, browse, create, edit, delete memories with full-text search |
| **Memory Graph** | Interactive force-directed visualization of knowledge connections |
| **Workflows** | Enable/disable, schedule, run workflows with one click |
| **Workflow Builder** | Visual drag-and-drop node editor for custom workflows |
| **Teams** | Create teams from agents, assign tasks, run and view results |
| **Flows** | Flow visualization, state inspection, and execution monitoring |
| **Ingest** | Upload and ingest documents (PDF, CSV, Excel, etc.) into brain memory |
| **MCP Hub** | MCP server registry, tool browser, and connection management |
| **Approvals** | Human-in-the-loop approval queue with approve/reject |
| **Chat** | Real-time chat with built-in commands (/status, /approvals, /schedule, /costs) |
| **Activity** | Real-time activity timeline with WebSocket auto-updates |
| **Settings** | API keys, provider testing, deep health checks |
| **Costs** | Per-model, per-workflow, per-day LLM cost tracking with budgets |
| **Logs** | Terminal-style real-time log viewer with filtering |
| **Skills** | Browse, install, and manage agent skills |
| **API Playground** | Postman-style endpoint tester with 17 pre-configured endpoints |
| **Backups** | Create, restore, download memory snapshots |
| **Webhooks** | Register incoming webhooks with optional HMAC validation |
| **Integrations** | Central view of 12 connected services |

### Keyboard Shortcuts
- `Ctrl+K` — Command palette (search across pages, actions, memories, workflows)
- `?` — Show all keyboard shortcuts
- Desktop notifications for pending approvals

### Mobile
AIBrain works as a Progressive Web App (PWA). Install on your phone, pair via QR code, approve actions with one tap.

---

## 8. API Reference

AIBrain exposes 165+ REST/WebSocket endpoints. Full OpenAPI docs available at `/docs` when running the server.

### Key Endpoint Groups
| Group | Endpoints | Description |
|-------|-----------|-------------|
| `/api/agent/memories` | 11 | CRUD, search, graph, import/export |
| `/api/agent/crons` | 4 | Scheduled job management |
| `/api/agent/workflows` | 6 | Workflow enable/disable/run |
| `/api/agent/approvals` | 6 | Approval queue |
| `/api/agent/chat` | 3 | Chat messages (REST + WebSocket) |
| `/api/agent/peers` | 5 | Multi-agent mesh |
| `/api/agent/skills` | 4 | Skill management |
| `/api/agent/tasks` | 4 | Task queue |
| `/api/agent/backups` | 5 | Backup management |
| `/api/agent/costs` | 2 | LLM cost tracking |
| `/webhooks` | 5 | Webhook management |
| `/health` | 2 | Health checks (standard + deep) |
| `/ws/chat` | 1 | WebSocket real-time chat |
| `/ws/notifications` | 1 | WebSocket notifications |

All endpoints work without WebSocket — REST fallbacks are available for every operation.

---

## 9. MCP Servers

AIBrain ships with **9 MCP servers**, making it the most comprehensive agent toolkit available. Every server is agent-agnostic — works with Claude, GPT, Cursor, Copilot, Ollama, or any MCP-compatible runtime.

### Setup
```bash
# Add all servers to Claude Code
aibrain mcp install

# Or add individually
claude mcp add aibrain-memory -- python mcp_server.py --db aibrain.db
claude mcp add it-support -- python mcp_it_support.py
claude mcp add peer-discovery -- python mcp_peer_discovery.py
```

### Server Overview

| Server | Tools | Tier | Description |
|--------|-------|------|-------------|
| **aibrain-memory** | 7 | Free | Persistent memory with selective routing (SelRoute) |
| **web-search** | 5 | Free | Zero-config web search (DuckDuckGo, optional SearXNG) |
| **it-support** | 9 | Pro | System diagnostics, remediation, ticket management |
| **peer-discovery** | 15 | Pro | Multi-agent mesh, messaging, file transfer, brain sync |
| **calendar** | 8 | Free/Pro | CalDAV + local .ics calendar management |
| **design** | 7 | Pro | Programmatic image creation (social posts, banners) |
| **desktop** | 10 | Pro | GUI automation (click, type, screenshot, window control) |
| **files** | 10 | Free/Pro | File search, organize, classify, deduplicate |
| **computer-use** | 1 | Pro | Full computer-use via MCP Control |

### Memory Server (aibrain-memory)
| Tool | Description |
|------|-------------|
| `memory_store` | Store a memory with auto-enrichment |
| `memory_search` | Semantic search with SelRoute (auto-detects query type) |
| `memory_recall` | Load top memories by importance score |
| `memory_forget` | Remove a specific memory |
| `memory_prepare` | Batch prepare memories for import |
| `memory_ingest` | Bulk import prepared memories |
| `memory_stats` | Database statistics and health |

### IT Support Server
| Tool | Description |
|------|-------------|
| `diagnose_network` | Network connectivity diagnosis |
| `diagnose_system` | CPU, RAM, disk health check |
| `check_services` | Service status monitoring |
| `check_ports` | Port availability scanning |
| `quick_fix` | Common automated remediation |
| `diagnose_full` | Complete system diagnostic |
| `ticket_create` | Create support ticket |
| `ticket_list` | List open tickets |
| `ticket_resolve` | Resolve and close a ticket |

### Peer Discovery Server
| Tool | Description |
|------|-------------|
| `peer_register` | Join the agent mesh |
| `peer_heartbeat` | Signal agent is alive |
| `peer_set_summary` | Update what you're working on |
| `peer_list` | Discover active peers (filter by scope) |
| `peer_send_message` | Send persistent message to a peer |
| `peer_check_messages` | Check incoming messages |
| `peer_broadcast` | Message all peers |
| `peer_unregister` | Leave the mesh |
| `peer_status` | Broker health check |
| `peer_send_file` | Send any file to a peer |
| `peer_list_files` | List incoming file transfers |
| `peer_receive_file` | Download a received file (SHA256 verified) |
| `peer_sync_brain` | Push your brain DB to a peer |
| `peer_share_skill` | Share a learned skill with a peer |
| `peer_deploy_config` | Push config files to a peer |

### Web Search Server
| Tool | Description |
|------|-------------|
| `web_search` | General web search |
| `web_search_news` | News-focused search |
| `web_search_images` | Image search |
| `web_fetch` | Fetch and parse a webpage |
| `web_search_and_summarize` | Search + AI-summarized results |

### Calendar Server
| Tool | Description |
|------|-------------|
| `list_events` | List calendar events |
| `create_event` | Create a new event |
| `update_event` | Modify an existing event |
| `delete_event` | Remove an event |
| `search` | Search events by keyword |
| `today` | Today's schedule |
| `upcoming` | Next 7 days |
| `export_ics` | Export calendar to .ics file |

### Design Server
| Tool | Description |
|------|-------------|
| `design_from_template` | Create design from HTML template |
| `design_social_post` | Generate social media image (1200x630) |
| `design_banner` | Create banner image (1920x480) |
| `design_thumbnail` | Create thumbnail (400x400) |
| `image_resize` | Resize any image |
| `image_text_overlay` | Add text to an image |
| `image_convert` | Convert between formats |

### Desktop Automation Server
| Tool | Description |
|------|-------------|
| `screenshot` | Capture screen or window |
| `click` | Click at coordinates or element |
| `type` | Type text into focused element |
| `hotkey` | Send keyboard shortcuts |
| `move` | Move mouse cursor |
| `find_text` | Find text on screen (OCR) |
| `list_windows` | List all open windows |
| `focus_window` | Bring window to front |
| `scroll` | Scroll in any direction |
| `wait_for` | Wait for element/text to appear |

### File Management Server
| Tool | Description |
|------|-------------|
| `files_search` | Fuzzy filename search |
| `files_search_content` | Full-text content search |
| `files_info` | Detailed file metadata |
| `files_organize` | Auto-organize files by type/date |
| `files_find_duplicates` | Find duplicate files (hash-based) |
| `files_recent` | Recently modified files |
| `files_large` | Find large files consuming disk |
| `files_watch` | Monitor directory for changes |
| `files_classify` | Auto-classify file type/category |
| `files_tree` | Directory tree visualization |

### Embedding Modes
| Mode | Size | NDCG@5 | Setup |
|------|------|--------|-------|
| No ML | 0 | 0.692 | Default — FTS5 only |
| MiniLM | 22MB | 0.796 | `pip install sentence-transformers` |
| bge-base | 110MB | 0.812 | Set env `AIBRAIN_EMBEDDING_MODEL=BAAI/bge-base-en-v1.5` |

AIBrain gracefully degrades — it works without ML dependencies, without embeddings, without sqlite-vec.

---

## 10. Networking & Tailscale Mesh

AIBrain uses Tailscale for secure, zero-config networking between agents. Every device on the mesh gets a stable private IP that works across NAT, firewalls, and networks — no port forwarding required.

### Why Tailscale
- **Encrypted by default** — WireGuard under the hood
- **NAT traversal** — works behind any router without port forwarding
- **Stable addresses** — each device gets a 100.x.x.x IP and MagicDNS hostname
- **Cross-platform** — Windows, macOS, Linux, iOS, Android
- **Zero config** — install, authenticate, connected

### Setup
```bash
# During AIBrain setup (auto-detected)
python setup_wizard.py

# Manual setup
tailscale up                    # Authenticate
python aibrain_networking.py    # Verify connectivity
```

The setup wizard auto-detects Tailscale, discovers peers on your tailnet, and configures peer discovery URLs automatically.

### What It Enables
| Feature | Description | Tier |
|---------|-------------|------|
| **Agent messaging** | Persistent SQLite-backed messages between agents | Pro |
| **File transfer** | Push files directly between machines (SHA256 verified) | Pro |
| **Brain sync** | Push/pull entire memory databases between devices | Pro |
| **Skill sharing** | Send learned skills from one agent to another | Pro |
| **Config deployment** | Push configs to multiple agents at once | Pro |
| **Log shipping** | Agents send diagnostics to a central peer | Pro |
| **Team mesh** | Multiple users' agents on same tailnet | Team |

### API Endpoints
```
GET  /api/agent/networking/status  — Full Tailscale status + peers
GET  /api/agent/networking/peers   — List online Tailscale peers
```

### Environment Variables
| Variable | Purpose |
|----------|---------|
| `PEER_BROKER_URL` | Remote peer broker URL (auto-set with Tailscale) |
| `PEER_BROKER_PORT` | Local broker port (default: 7800) |

---

## 11. Multi-Agent Mesh

Multiple AIBrain instances can coordinate as a mesh network.

### How It Works
1. **Register** agents by pointing to their database files
2. **Sync** exports, merges, and reimports across all agents
3. **Consensus** weights principles by agreement — if 3/4 agents agree, confidence increases
4. **Conflict resolution** resolves contradictions by consensus or recency
5. **Distribution** pushes the merged brain back to all agents

### Commands
```bash
aibrain mesh status                    # See all registered agents
aibrain mesh register case /path/to/case.db  # Add an agent
aibrain mesh merge                     # Merge all agents
aibrain mesh distribute                # Push merged brain to all

# Swarm training — spin up N experiments
aibrain mesh swarm create security -n 100  # Create 100 training variants
aibrain mesh swarm harvest security        # Merge best results back
```

### Brain Versioning
```bash
aibrain history snapshot "before experiment"  # Save current state
aibrain history list                          # See all snapshots
aibrain history rollback <id>                 # Undo to snapshot
aibrain history branch "experiment-a"         # Branch for testing
aibrain history diff <a> <b>                  # Compare versions
```

### Brain Scaling
AIBrain supports hot/archive tiering for large brains:
- **Hot tier** — Active memories in primary DB (fast FTS5)
- **Archive tier** — Old/low-importance memories in separate DB
- **Auto-promotion** — Archived memories resurface when queried
- **Compaction** — Periodic move from hot to archive based on decay score

```bash
aibrain scale stats      # Health report
aibrain scale compact    # Archive stale memories
aibrain scale analyze    # Get shard recommendations
```

---

## 11b. Boss Agent Orchestrator (Pro/Team)

The Boss Agent lets a single host agent coordinate multiple Docker worker agents in parallel. Each worker runs in its own isolated container with optional Playwright browser support. The boss assigns tasks via Redis queues, monitors health, collects structured results, and merges outputs into the shared brain.

### Quick Start

```bash
pip install aibrain[boss]
docker compose -f docker-compose.boss.yml up -d --scale worker=2

python -m boss.boss assign --type shell --cmd "echo hello"
python -m boss.boss status
python -m boss.boss results
```

### Worker Types

| Type | Use case |
|------|----------|
| `shell` | CLI commands, scripts, file processing |
| `python` | Python code with full package access |
| `playwright` | Browser automation — form filling, scraping, OAuth flows |

### Model Tiers

Route tasks to the right model based on complexity:

- **opus** — complex reasoning, security audits, orchestration
- **sonnet** — general analysis, code review, writing
- **haiku** — monitoring, status checks, simple lookups

### Scaling

```bash
docker compose -f docker-compose.boss.yml up -d --scale worker=4
```

### Teardown

```bash
python -m boss.boss teardown
```

Workers are disposable — they write to session files only. The boss merges results into the shared brain after collection. No worker ever gets direct write access to `aibrain.db`.

See `boss/SKILL.md` and `boss/workflows/docker-setup.md` for full documentation.

---

## 12. Agent/Task/Team System

Build multi-agent systems with typed composition. Define agents, give them tasks, and run them as teams.

### Agents

An agent has a role, goal, and backstory that shape its behavior:

```python
from aibrain import Agent

researcher = Agent(
    role="Research Analyst",
    goal="Find accurate, current information on any topic",
    backstory="Senior analyst with 10 years of market research experience",
    verbose=True,
)
```

### Tasks

A task describes what needs to be done and which agent does it:

```python
from aibrain import Task

task = Task(
    description="Research the current state of MCP adoption across AI tools",
    expected_output="A structured summary with key findings, adoption rates, and trends",
    agent=researcher,
)
```

### Teams

A team coordinates agents and tasks with a chosen process:

```python
from aibrain import Agent, Task, Team

researcher = Agent(role="Researcher", goal="Find facts", backstory="Expert researcher")
writer = Agent(role="Writer", goal="Write reports", backstory="Technical writer")

research = Task(description="Research MCP adoption", expected_output="Summary of findings", agent=researcher)
report = Task(description="Write a briefing", expected_output="500-word report", agent=writer)

team = Team(
    agents=[researcher, writer],
    tasks=[research, report],
    process="sequential",  # tasks run in order
)

result = team.kickoff()
print(result.raw)           # final output text
print(result.tasks_output)  # per-task results
```

### Process Types

| Process | How it works |
|---------|-------------|
| `sequential` | Tasks execute in order. Each task can use the output of the previous one. |
| `hierarchical` | A manager agent delegates tasks to workers and synthesizes results. |

### Delegation

Agents can delegate to other agents in the team when the task requires different expertise. Enable with `allow_delegation=True` on the agent.

### Guardrails

Tasks support guardrails — validation functions that check output quality. If a guardrail fails, the task retries automatically up to `max_retries` times.

```python
def must_contain_data(output):
    return "data" in output.raw.lower()

task = Task(
    description="Produce a data-driven analysis",
    expected_output="Analysis with numerical data",
    agent=researcher,
    guardrails=[must_contain_data],
    max_retries=3,
)
```

---

## 13. Flow Engine

Build complex, stateful workflows with decorators. Flows are state machines where methods are nodes and decorators define the execution graph.

### Basic Flow

```python
from aibrain.core.flow import Flow, start, listen
from pydantic import BaseModel

class CounterState(BaseModel):
    count: int = 0
    message: str = ""

class CounterFlow(Flow[CounterState]):

    @start()
    def begin(self):
        self.state.count = 1
        return self.state.count

    @listen(begin)
    def increment(self):
        self.state.count += 1
        self.state.message = f"Count is {self.state.count}"
        return self.state.message

flow = CounterFlow()
result = flow.kickoff()
```

### Conditional Routing

Use `@router` to branch execution based on state:

```python
from aibrain.core.flow import Flow, start, listen, router

class ReviewFlow(Flow[ReviewState]):

    @start()
    def analyze(self):
        self.state.score = evaluate(self.state.document)
        return self.state.score

    @router(analyze)
    def decide(self):
        if self.state.score >= 0.8:
            return "approve"
        return "revise"

    @listen("approve")
    def publish(self):
        return "Published"

    @listen("revise")
    def send_back(self):
        return "Needs revision"
```

### Features

| Feature | Description |
|---------|-------------|
| **Typed state** | Pydantic models for flow state — validated, serializable |
| **@start** | Entry point(s) for the flow. Optional condition parameter. |
| **@listen** | Executes when a specific method completes or a named route is taken |
| **@router** | Returns a string to select which `@listen` branch fires next |
| **@retry** | Automatic retry with configurable attempts and backoff |
| **@timeout** | Fail a step if it exceeds a time limit |
| **Persistence** | Save and restore flow state for long-running processes |
| **HITL** | Pause a flow for human input, resume later |
| **Visualization** | Generate a visual graph of the flow's execution paths |

---

## 14. Document Ingestion

Feed documents into brain memory. AIBrain extracts text, chunks it intelligently, and stores each chunk as a searchable memory.

### Usage

```python
from aibrain import Brain

brain = Brain()

# Single file
brain.ingest("quarterly_report.pdf")
brain.ingest("sales_data.csv")
brain.ingest("architecture.md")

# Excel workbook
brain.ingest("financials.xlsx")

# JSON / JSONL
brain.ingest("events.jsonl")

# Entire directory (recursively)
brain.ingest("/path/to/documents/")

# Search across all ingested content
results = brain.search("revenue growth Q1")
```

### Supported Formats

| Format | Extension | Notes |
|--------|-----------|-------|
| PDF | .pdf | Requires `pdfplumber` (`pip install aibrain[ingest]`) |
| CSV | .csv | Each row becomes a memory |
| Excel | .xlsx | Requires `openpyxl` (`pip install aibrain[ingest]`) |
| JSON | .json | Supports nested structures |
| JSONL | .jsonl | One JSON object per line |
| Text | .txt | Plain text with sentence-aware chunking |
| Markdown | .md | Preserves heading structure |
| Directory | (folder) | Recursively ingests all supported files |

### Chunking

Documents are split into chunks using sentence-aware boundaries. Configurable parameters:

- **chunk_size** — Target size per chunk (default: 512 tokens)
- **chunk_overlap** — Overlap between consecutive chunks for context continuity
- **collection** — Organize ingested content into named collections

### Incremental Ingestion

Re-running `brain.ingest()` on the same file skips content that has already been ingested. Only new or modified content is processed.

### Install Dependencies

```bash
pip install aibrain[ingest]  # PDF + Excel support
```

---

## 14b. Universal MCP Client

Connect to any MCP server from Python. AIBrain's MCP client supports three transports and bridges remote tools into your local environment.

### Registry

```python
from aibrain.mcp import MCPServerRegistry, MCPServerStdio, MCPServerHTTP

registry = MCPServerRegistry()

# stdio transport — launches a subprocess
registry.register("github", MCPServerStdio(
    command="npx",
    args=["@github/mcp-server"],
    env={"GITHUB_TOKEN": "..."},
))

# HTTP transport — connects to a running server
registry.register("custom-api", MCPServerHTTP(
    url="http://localhost:9000/mcp",
))

# List registered servers
print(registry.list())
```

### Transports

| Transport | Class | Use case |
|-----------|-------|----------|
| **stdio** | `MCPServerStdio` | Launch a local subprocess (npx, python, etc.) |
| **HTTP** | `MCPServerHTTP` | Connect to a remote HTTP MCP endpoint |
| **SSE** | `MCPServerSSE` | Server-Sent Events streaming transport |

### Tool Bridging

Once registered, you can bridge remote MCP tools into local callable functions:

```python
from aibrain.mcp import MCPServerRegistry
from aibrain.mcp.tool_bridge import bridge_tools

registry = MCPServerRegistry()
# ... register servers ...

tools = bridge_tools(registry)
# tools is a dict of callable functions mapped from MCP tool definitions
```

### Dashboard

The **/mcp** dashboard page shows all registered servers, their connection status, available tools, and lets you manage the registry from the browser.

---

### Brain Marketplace (Conceptual)

Users will be able to package, sell, and share trained brains.

### Packaging
```bash
aibrain marketplace package --name "security-expert"
# Creates a .brain file (JSON + metadata)
# Quality gates: minimum principles, episodes, etc.
```

### Publishing & Discovery
```bash
aibrain marketplace validate my-brain.brain  # Validate quality
aibrain marketplace publish my-brain.brain   # List on marketplace
aibrain marketplace search "penetration testing"  # Find brains
aibrain marketplace install <id>             # Install a brain
aibrain marketplace revenue                  # Revenue dashboard
```

### Revenue Model
- **70/30 split** — Sellers keep 70%, platform takes 30%
- **Stripe Connect** integration for payouts
- **License key** generation for paid brains

---

## 15. Stack System

The stack system captures and restores your entire agent's running state.

### What It Captures
- Which workflows are scheduled (and at what times)
- Which hooks are active
- Which services are running
- Database statistics

### Operations
```bash
aibrain stack save trading       # Save named snapshot
aibrain stack save security      # Save another
aibrain stack restore trading    # Switch to trading stack
aibrain stack list               # See all snapshots
aibrain stack set-default        # Current state = boot default
```

### Auto-Restore
On every session start, AIBrain automatically restores from the `last` snapshot. This means your agent recovers its full state after any crash or reboot.

An hourly cron job (`stack_snapshot auto`) captures the current state, so the `last` snapshot is always recent.

---

## 16. Self-Learning & Evolution

AIBrain continuously improves itself through several mechanisms.

### Evolution Engine
The evolution loop runs automatically:
1. **Pattern Bus** emits domain events to SQLite
2. **Outcome Tracking** records what worked and what didn't
3. **Hypothesis Generation** creates testable improvement ideas
4. **Experiment Running** tests hypotheses with measurement
5. **Pattern Reinforcement** boosts validated patterns

### Reactive Workflow Engine
Inspired by the FlyWire fruit fly connectome, the reactive engine is state-driven rather than sequence-driven:
- **Forward chaining** — Nodes fire when prerequisites are met
- **Refraction** — Prevents same rule firing repeatedly
- **Priority-based conflict resolution** — Chooses which rule fires when multiple are ready
- **SQLite-backed checkpoints** — Recovery on restart

### Key Self-Learning Workflows
| Workflow | What it does |
|----------|-------------|
| `memory_consolidation` | Merges redundant memories into principles |
| `contradiction_detector` | Finds and resolves conflicting memories |
| `correction_learner` | Extracts lessons from user corrections |
| `evolution_experiment` | A/B tests improvement strategies |
| `evolution_auto_loop` | Full cycle: insights -> hypotheses -> experiments -> principles |
| `knowledge_graph_builder` | Builds entity/relationship graphs |
| `cross_domain_questions` | Finds connections across different domains |
| `session_continuity` | Verifies memories survived compaction |
| `goal_drift_detector` | Compares daily patterns against stated goals |

---

## 17. Skill Evolution

AIBrain tracks skills as versioned, living documents. Skills improve over time through execution feedback, automatic health monitoring, and evolution triggers.

### How It Works
Skills are stored as SKILL.md files with YAML frontmatter. Every version is tracked in a version DAG (directed acyclic graph) in SQLite:

```yaml
---
name: web_scraping
version: 3
trigger: periodic_health
parent_version: 2
---

# Web Scraping Skill
...
```

### Version DAG
Each skill version has:
- **Content snapshot** — full skill text at that version
- **Content diff** — what changed from the parent version
- **Parent version ID** — forming a DAG (supports branching)
- **Trigger** — what caused the evolution (post-execution, degradation, health check)

### Execution Tracking
Every time a skill runs:
- Duration, success/failure, and output are recorded
- Running averages for success rate and latency
- Degradation detection — if success rate drops, triggers evolution

### Workflows
| Workflow | Schedule | What it does |
|----------|----------|-------------|
| `skill_health_check` | Every 6 hours | Monitors success rates, flags degraded skills |
| `skill_evolution_audit` | Weekly | Reviews version history, identifies stale skills |
| `skill_auto_evolver` | Daily | Automatically evolves skills that need improvement |

### Commands
```bash
python run_workflow.py skill_health_check
python run_workflow.py skill_evolution_audit
python run_workflow.py skill_auto_evolver
```

### MCP Integration
Skill evolution integrates with the peer discovery mesh — use `peer_share_skill` to send evolved skills to other agents.

---

## 18. Auto-Experiment

Inspired by Andrej Karpathy's experiment loop: **hypothesize → modify → run → evaluate → keep or revert**. AIBrain automates this cycle for any measurable improvement task.

### How It Works
1. Create a `program.md` with YAML frontmatter specifying what to optimize
2. Run the experiment loop — AIBrain creates a git branch per run
3. Each iteration: modify the target file, run evaluation, measure the metric
4. Keep improvements, revert failures, log everything to `results.tsv`

### Program File Format
```yaml
---
mutable_file: src/config.py
eval_command: python run_eval.py
metric_name: accuracy
metric_direction: maximize
time_budget: 30m
max_loops: 10
---

# Optimization Goal
Improve the accuracy of the classification pipeline by tuning
hyperparameters in src/config.py.
```

### Usage
```bash
python workflows/auto_experiment.py init program.md       # Initialize experiment
python workflows/auto_experiment.py run-eval               # Run one evaluation
python workflows/auto_experiment.py decide                 # Keep or revert
python workflows/auto_experiment.py summary                # View all results
```

### Use Cases
- **SelRoute tuning** — optimize query routing thresholds
- **Prompt optimization** — A/B test prompt variations
- **Model config tuning** — temperature, top_p, batch sizes
- **Latency reduction** — measure and optimize hot paths
- **Any measurable metric** — if you can compute a number, you can auto-experiment

### Safety
- Every experiment runs on a dedicated git branch
- Each iteration is committed — full audit trail
- Configurable loop limits prevent runaway experiments
- Original code always preserved on main branch

---

## 19. IT Support

Built-in first-line IT diagnostics and automated remediation. Your agent can diagnose system issues, monitor services, and fix common problems — no separate IT tools needed.

### MCP Tools
9 tools available via the IT Support MCP server (see Section 9).

### Workflows
| Workflow | Schedule | What it does |
|----------|----------|-------------|
| `system_health_check` | Every 2 hours | CPU, RAM, disk, network health |
| `service_monitor` | Every 15 min | Check critical service status |
| `port_monitor` | Every 30 min | Verify expected ports are open |
| `network_diagnostic` | Every hour | Full network connectivity test |
| `full_diagnostic` | Daily | Complete system audit |
| `auto_remediate` | On-demand | Fix common issues automatically |
| `scheduled_maintenance` | Weekly | Cleanup temp files, update packages |
| `ticket_dashboard` | On-demand | Summary of open support tickets |

### Ticket System
IT Support includes a built-in ticketing system stored in your brain database:
- Create tickets for tracking issues
- Auto-link diagnostics to tickets
- Resolve with notes and resolution details
- Full history in the `it_tickets` table

### Cross-Platform
All diagnostics work on Windows, macOS, and Linux. Platform-specific checks auto-detect the OS and use appropriate commands.

---

## 20. Risk Analyst

A pre-action safety gate that evaluates proposed actions before execution.

### How It Works
Every action is scored across 4 dimensions:
1. **Reversibility** — Can this be undone? How long would recovery take?
2. **Blast radius** — Who/what is affected? Public, shared, or local?
3. **Data sensitivity** — Credentials? PII? Internal architecture?
4. **Precedent** — Has this exact action been done safely before?

### Risk Tiers
| Tier | When | Action |
|------|------|--------|
| **ESCALATE** | Any CRITICAL dimension | Flag strongly, recommend against proceeding |
| **PAUSE FOR APPROVAL** | 2+ HIGH dimensions | Present to user for decision |
| **PROCEED WITH CAUTION** | 1 HIGH dimension | Implement mitigations, then proceed |
| **PROCEED** | All LOW | Log only |

The risk analyst is **always advisory, never enforced** — the user always has the final say. It can be turned off per-workflow or globally.

### What It Catches
- Credentials in code being pushed to public repos
- File deletion without backup
- Force-push or history rewriting
- External communications with sensitive content
- Financial transactions

### Usage
```bash
# Assess a specific action
python workflows/risk_analyst.py --action "push to public repo" --content "API_KEY=sk-..."

# Retrospective scan of last 24h
python workflows/risk_analyst.py --retrospective --hours 24

# Scan file content
python workflows/risk_analyst.py --action "publish" --content-file config.json
```

---

## 21. Tier System & Pricing

### Free Tier
Everything you need for a fully functioning AI brain:
- Core memory system (store, search, recall, forget) — 7 MCP tools
- Web search — 5 MCP tools (DuckDuckGo, no API key needed)
- Basic file management — search, info, recent, tree
- Local calendar management (.ics files)
- 22 self-learning workflows
- Full CLI and MCP integration
- Brain import/export
- Stack snapshots
- Dashboard UI + PWA mobile access
- Productivity brain pack
- Approval queue for human-in-the-loop

### Pro Tier — $9.95/mo
The full agent operating system:
- **Everything in Free**, plus:
- **IT Support** — 9 diagnostic tools + 8 workflows + ticketing system
- **Peer Discovery** — 15 MCP tools for multi-agent mesh, messaging, file transfer
- **Tailscale Networking** — encrypted mesh between your devices, brain sync, skill sharing
- **Desktop Automation** — 10 tools for GUI control (screenshot, click, type, window management)
- **Design** — 7 tools for programmatic image creation (social posts, banners, thumbnails)
- **Full Calendar** — CalDAV integration with Google/Outlook/iCloud/Nextcloud
- **Full File Management** — organize, classify, deduplicate, watch for changes
- **Skill Evolution** — version DAG, health monitoring, auto-evolution
- **Auto-Experiment** — Karpathy-inspired optimization loops for any metric
- **CLI-Anything Bridge** — auto-discover and control desktop apps via CLI
- **Content Generation** — 10 platforms (LinkedIn, X, YouTube, etc.)
- **Multi-Agent Ops** — agent sync, memory diff, cross-agent evolution
- **Cloud Sync** — sync brain across devices
- **Advanced Analytics** — usage patterns, memory health, workflow performance
- **Marketplace Access** — buy and sell trained brains

### Team Tier — $29.95/mo
For teams running multiple agents:
- **Everything in Pro**, plus:
- **Team Brains** — shared knowledge across team members' agents
- **Mesh Merge** — consensus-weighted brain merging across 10+ agents
- **Multi-User** — multiple people's agents on the same tailnet
- **Team Workflows** — coordinated workflows across the team

### Enterprise — $99/mo
For organizations:
- **Everything in Team**, plus:
- Unlimited agents
- Swarm training
- Priority support
- Custom embedding models

### Marketplace
- Sell your trained brains to other users
- 70/30 revenue split (seller/platform)
- Stripe Connect for payouts

---

## 22. Mobile (PWA)

AIBrain's dashboard is a Progressive Web App — install it on your phone for native-app-like access to your agent from anywhere.

### Installation
1. Open your AIBrain dashboard URL in your phone's browser (Chrome, Safari, Firefox)
2. Tap "Add to Home Screen" (or "Install App")
3. AIBrain appears as an app icon on your home screen

### Features
- **Standalone mode** — full-screen, no browser chrome
- **Offline access** — cached pages work without internet
- **Push notifications** — approval requests, workflow completions, agent status (Pro)
- **App shortcuts** — quick launch to Memories, Workflows, or Chat
- **Auto-update** — new versions deploy instantly via service worker

### What Works on Mobile
| Feature | Status |
|---------|--------|
| Memory search & browse | Full |
| Workflow monitoring | Full |
| Chat with agent | Full |
| Approval queue | Full + push notifications |
| Settings | Full |
| Network status | Full |
| File management | View + search |
| IT Support tickets | Full |

### Push Notifications (Pro)
The service worker handles push notifications for:
- **Approval requests** — with Approve/Deny action buttons
- **Workflow completions** — tap to see results
- **Agent status changes** — online/offline alerts
- **Peer messages** — incoming messages from other agents

### Tailscale on Mobile
Install the Tailscale app on your phone (iOS/Android) to make your phone a node on the mesh. Your phone's agent can then communicate directly with your desktop/VPS agents.

---

## 23. Installation & Updates

### Requirements
- Python 3.10+
- Node.js 18+ (optional, for dashboard UI)
- Git (optional, for auto-updates)
- Tailscale (optional, for mesh networking — Pro tier)

### Install Methods
```bash
# From source (recommended)
git clone https://github.com/sindecker/aibrain.git
cd aibrain && python install.py

# From PyPI
pip install aibrain-core

# Portable (Windows)
python install.py --portable
```

### What Install Does
1. Checks Python and Node.js versions
2. Creates virtual environment
3. Installs dependencies
4. Migrates any existing database (auto-detects brain.db, memory.db, agent.db, etc.)
5. Initializes database schema
6. Creates launch scripts
7. Runs auto-setup (discovers env vars, creates config)
8. Enables free-tier workflows
9. Offers brain pack selection
10. Saves initial stack snapshot

### Updates
```bash
aibrain update           # Safe update (backup first)
aibrain update --dry-run # Preview changes
```

Updates automatically:
- Back up your database and config
- Pull latest code (git) or upgrade package (pip)
- Migrate database if schema changed
- Sync agent hooks
- Enable any new free-tier workflows
- Validate nothing broke

### Database Migration
AIBrain auto-migrates from any prior system on install or update. It scans for any `.db` file containing a `memories` table and imports it. Supported legacy names:
- `agentos.db` (legacy)
- `brain.db`, `memory.db`, `agent.db`
- Any custom-named SQLite with a `memories` table

The old database is backed up as `<name>.migrated`, never deleted.

---

## 24. Deployment

### Docker
```bash
docker compose up -d
# Dashboard at :5173, API at :8001, Peer broker at :7800
```

Features:
- Multi-stage build (Python 3.11 + Node 20)
- Health checks every 30 seconds
- Nginx frontend serving
- Data persistence via volumes
- Tailscale mesh networking (Pro)

### VPS / Headless
For agent-to-agent deployments without a browser UI:
```bash
aibrain mcp                                              # MCP server only
aibrain server --no-ui                                   # API only, no frontend
python mcp_peer_discovery.py --broker-only --port 7800   # Peer broker only
```

### Multi-Machine (Tailscale)
```bash
# On each machine:
tailscale up                    # Join tailnet
python setup_wizard.py          # Auto-discovers peers
python mcp_peer_discovery.py --broker-only --port 7800

# Agents communicate directly over 100.x.x.x addresses
# No port forwarding, no public exposure, encrypted by default
```

### Systemd Service
```ini
[Unit]
Description=AIBrain Agent
After=network.target

[Service]
ExecStart=/opt/aibrain/venv/bin/python mcp_server.py --db /opt/aibrain/aibrain.db
Restart=on-failure
User=aibrain

[Install]
WantedBy=multi-user.target
```

---

## 25. Configuration

### config.json
Core configuration file — created during install, never overwritten by updates.

Key fields:
```json
{
  "agent_name": "Your Agent",
  "memory_db": "aibrain.db",
  "email_to": "you@example.com",
  "location_name": "Your City",
  "location_lat": 0,
  "location_lon": 0
}
```

### Environment Variables
| Variable | Purpose |
|----------|---------|
| `AIBRAIN_DB` | Override database path |
| `AIBRAIN_ROOT` | Override install directory |
| `AIBRAIN_TIER` | Override tier (dev/testing only) |
| `AIBRAIN_LICENSE_KEY` | License key for Pro/Team/Enterprise |
| `AIBRAIN_EMBEDDING_MODEL` | Embedding model (default: MiniLM) |
| `ANTHROPIC_API_KEY` | Claude API key |
| `OPENAI_API_KEY` | GPT API key |
| `PEER_BROKER_URL` | Remote peer broker URL |
| `PEER_BROKER_PORT` | Local broker port (default: 7800) |
| `CALDAV_URL` | CalDAV server URL (for full calendar) |
| `CALDAV_USER` | CalDAV username |
| `CALDAV_PASS` | CalDAV password |
| `SEARXNG_URL` | SearXNG instance URL (optional, for web search) |

### CLI Config
```bash
aibrain config get agent_name
aibrain config set agent_name "MyAgent"
```

---

## 26. Troubleshooting

### Database Issues
**Two .db files exist (e.g., legacy agentos.db and aibrain.db)**
Run `aibrain update` — it auto-migrates the larger database to `aibrain.db` and backs up the old one.

**MCP server can't find database**
Check the `--db` path in your MCP configuration. Run `aibrain status` to see the active database path.

**Database locked**
SQLite WAL mode handles concurrent reads. If you get a lock error, ensure only one write process is active. Restart the MCP server if needed.

### Workflow Issues
**Workflows not running**
1. Check `aibrain workflows list` — is the workflow enabled?
2. Check `aibrain stack restore` — were cron jobs recreated?
3. Run `aibrain workflows run <name>` manually to test

**Missing workflows after update**
Run `aibrain update` — it auto-enables new free-tier workflows.

### Stack Issues
**Agent lost its state after restart**
Run `aibrain stack restore last` — the hourly auto-snapshot should have recent state.

**Stack restore fails**
Check `aibrain stack list` for available snapshots. Try restoring from a named snapshot instead of `last`.

### Connection Issues
**Dashboard won't load**
Check `aibrain server` is running. Default port is 8001. Check firewall rules if accessing remotely.

**MCP server disconnects**
Restart the MCP server. Check Python path and database path are correct.

---

## Feature Count Summary

| Category | Count |
|----------|-------|
| MCP servers | 9 |
| MCP tools (total) | 72 |
| Dashboard pages | 45+ |
| Workflow scripts | 200+ |
| CLI commands | 50+ |
| API endpoints | 170+ |
| Brain packs | 8 |
| Free-tier features | Memory, web search, basic files, local calendar, dashboard, PWA, approval queue |
| Pro-tier features | IT support, peer mesh, Tailscale networking, desktop automation, design, full calendar, full files, skill evolution, auto-experiment, CLI-Anything, content generation, multi-agent ops, marketplace |
| Team-tier features | Team brains, mesh merge, multi-user, team workflows |

---

*AIBrain is proprietary software. All rights reserved.*
*Built by DeckerOps — https://myaibrain.org*
