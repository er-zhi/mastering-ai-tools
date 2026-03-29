# Ruflo Swarm Setup Design

**Date:** 2026-03-29
**Project:** mastering-ai-tools
**Goal:** Install ruflo inside the project and use its hierarchical swarm to plan and build the Tic-Tac-Toe pet project.

---

## Context

This project is a learning sandbox for AI-assisted development. Ruflo (github.com/ruvnet/ruflo) is a multi-agent orchestration layer on top of Claude Code that supports 6 swarm topologies. We are using it to practice swarm-based planning and implementation.

---

## Setup

- Clone ruflo flat into `./ruflo/` (not a submodule)
- Add `ruflo/` to `.gitignore` — not tracked in this repo
- Install deps: `cd ruflo && npm install`
- Ruflo config (`.ruflo/config.json`) is kept in the repo for reproducibility

---

## Swarm Topology: Hierarchical

The hierarchical topology is used exclusively. A queen agent coordinates specialist worker agents.

```
Queen Agent
├── Architect Agent      → system design document
├── Backend Agent        → FastAPI spec (endpoints, data model, game logic)
├── Frontend Agent       → Next.js component plan
└── API Contract Agent   → gRPC-Web proto definitions
```

Queen receives the project brief, decomposes it into subtasks, assigns each specialist, waits for their outputs, and synthesizes a final architecture document.

### Agent → Skill + Model mapping

| Agent | Model | Skills |
|---|---|---|
| **Queen** | Sonnet | `dispatching-parallel-agents` — assigns tasks to workers<br>`writing-plans` — synthesizes outputs into implementation plan<br>`subagent-driven-development` — coordinates parallel execution<br>`ci-cd-pipeline-builder` — generate GitHub Actions pipeline (post-build)<br>`dependency-auditor` — CVE + license scanning (post-build) |
| **Architect** | Sonnet | `architecture-patterns` — Clean Architecture, Hexagonal, DDD<br>`brainstorming` — explores design options before committing |
| **Backend** | Haiku | `python-design-patterns` — KISS, SRP, composition over inheritance<br>`test-driven-development` — spec tests before logic<br>`systematic-debugging` — diagnose issues in game logic<br>`database-designer` — SQLite schema, migrations, indexing |
| **Frontend** | Haiku | `test-driven-development` — component tests first<br>`systematic-debugging` — diagnose rendering/state issues |
| **API Contract** | Haiku | `architecture-patterns` — interface and boundary design<br>`verification-before-completion` — verify proto contracts before handoff |

Sonnet for high-complexity reasoning (orchestration, architecture decisions). Haiku for medium-complexity spec and plan generation — ~15x cheaper per token.

---

## Cost Efficiency Configuration

### Environment

```bash
CLAUDE_CODE_ENABLE_TELEMETRY=1          # track cost per agent
CLAUDE_FLOW_TOOL_GROUPS=core,dev        # load only needed tool categories
CLAUDE_FLOW_HOOKS_ENABLED=true          # enable Agent Booster signals
```

### Ruflo config (`.ruflo/config.json`)

```json
{
  "swarm": {
    "topology": "hierarchical",
    "maxAgents": 6,
    "cacheProfile": "multi-agent",
    "consensus": "raft"
  },
  "routing": {
    "tiers": {
      "wasm":   { "complexityMax": 10,  "cost": "$0",     "latency": "<1ms"   },
      "haiku":  { "complexityMax": 30,  "cost": "$0.0002","latency": "~500ms" },
      "sonnet": { "complexityMax": 100, "cost": "$0.003", "latency": "2-5s"   }
    }
  },
  "agents": {
    "queen":       { "model": "sonnet", "priority": "high" },
    "architect":   { "model": "sonnet", "priority": "high" },
    "backend":     { "model": "haiku",  "priority": "medium" },
    "frontend":    { "model": "haiku",  "priority": "medium" },
    "api-contract":{ "model": "haiku",  "priority": "medium" }
  }
}
```

### Cost saving mechanisms active

| Mechanism | Saving | Notes |
|---|---|---|
| 3-tier model routing | up to 75% API cost | Haiku for 3 of 5 agents |
| Agent Booster (WASM) | $0 for simple transforms | Triggers on `[AGENT_BOOSTER_AVAILABLE]` signal |
| Token optimizer | 30-50% tokens | Pattern retrieval + caching + batching |
| ICOS cache (`multi-agent`) | 65-90% tokens | Prevents context compaction in swarm sessions |
| `maxAgents: 6` | reduces idle agent cost | No unnecessary worker spawning |

### Monitoring

```bash
# After a swarm run
analysis claude-cost          # session cost breakdown
analysis token-usage          # per-agent token consumption
```

---

## First Swarm Run: Architecture Planning

**Input:** Project brief:
- Backend: Python 3.14 + FastAPI + SQLite
- Frontend: Next.js + 3D GPU-accelerated rendering
- Transport: Docker + gRPC-Web (Next.js client ↔ Python server)
- App: Tic-Tac-Toe

**Output:** `docs/architecture.md` containing:
- System design overview
- Component breakdown
- API contracts (gRPC-Web proto definitions)
- Data model (SQLite schema)
- Frontend component tree

---

## Workflow

```
brief
  → queen-led swarm (ruflo hierarchical, cost-optimised)
      → docs/architecture.md
  → writing-plans skill
      → implementation plan
  → subagent-driven-development skill
      → code
```

Each phase is a separate session. The swarm output feeds the next skill.

---

## What is NOT in scope

- Ruflo self-learning / SONA features (advanced, skip for now)
- Multi-LLM routing (use Claude only)
- Other swarm topologies (mesh, ring, etc.) — revisit after hierarchical is understood
- Opus tier — not needed for this project scale
