# Ruflo Swarm Setup Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Install ruflo, configure a cost-optimised hierarchical swarm, and run the first planning session to produce `docs/architecture.md` for the Tic-Tac-Toe project.

**Architecture:** Ruflo cloned flat into `./ruflo/` (gitignored), configured via `.ruflo/config.json` (tracked). A queen agent (Sonnet) orchestrates four specialist workers (Architect on Sonnet, Backend/Frontend/API Contract on Haiku) to produce architecture documents. Cost savings come from 3-tier model routing, ICOS multi-agent cache, and limiting maxAgents to 6.

**Tech Stack:** Node.js 20+, ruflo v3.5+, Claude Sonnet 4.6, Claude Haiku 4.5

---

## File Structure

| File | Action | Purpose |
|---|---|---|
| `ruflo/` | Created by clone | Ruflo source — gitignored |
| `.ruflo/config.json` | Create | Swarm + routing + agent config — tracked |
| `.env.ruflo` | Create | Telemetry env vars — gitignored |
| `docs/architecture.md` | Created by swarm | Tic-Tac-Toe architecture output |

---

### Task 1: Clone ruflo and install dependencies

**Files:**
- Create: `ruflo/` (gitignored)

- [ ] **Step 1: Clone ruflo into the project root**

```bash
git clone https://github.com/ruvnet/ruflo ruflo
```

Expected output: `Cloning into 'ruflo'...` followed by clone progress. Final line: `Resolving deltas: 100%`

- [ ] **Step 2: Install Node dependencies**

```bash
cd ruflo && npm install
```

Expected: packages installed with no audit errors. If there are high severity CVEs, note them but continue — this is a learning project.

- [ ] **Step 3: Verify ruflo CLI is available**

```bash
cd ruflo && npx ruflo --version
```

Expected: prints ruflo version string e.g. `ruflo/3.5.x`

- [ ] **Step 4: Run health check**

```bash
npx ruflo doctor --fix
```

Expected: all checks pass or auto-fixed. Note any failures — they'll affect the swarm run.

- [ ] **Step 5: Confirm ruflo/ is gitignored**

```bash
cd .. && git status
```

Expected: `ruflo/` does NOT appear in untracked files. If it does, verify `.gitignore` contains `ruflo/`.

---

### Task 2: Create ruflo config

**Files:**
- Create: `.ruflo/config.json`

- [ ] **Step 1: Create the .ruflo directory**

```bash
mkdir -p .ruflo
```

- [ ] **Step 2: Write the swarm config**

Create `.ruflo/config.json`:

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
      "wasm":   { "complexityMax": 10,  "cost": "$0",      "latency": "<1ms"   },
      "haiku":  { "complexityMax": 30,  "cost": "$0.0002", "latency": "~500ms" },
      "sonnet": { "complexityMax": 100, "cost": "$0.003",  "latency": "2-5s"   }
    }
  },
  "agents": {
    "queen":        { "model": "claude-sonnet-4-6", "priority": "high"   },
    "architect":    { "model": "claude-sonnet-4-6", "priority": "high"   },
    "backend":      { "model": "claude-haiku-4-5",  "priority": "medium" },
    "frontend":     { "model": "claude-haiku-4-5",  "priority": "medium" },
    "api-contract": { "model": "claude-haiku-4-5",  "priority": "medium" }
  }
}
```

- [ ] **Step 3: Verify config parses correctly**

```bash
node -e "const c = require('./.ruflo/config.json'); console.log('OK:', c.swarm.topology)"
```

Expected: `OK: hierarchical`

- [ ] **Step 4: Commit the config**

```bash
git add .ruflo/config.json
git commit -m "feat: add ruflo hierarchical swarm config"
```

---

### Task 3: Configure telemetry

**Files:**
- Create: `.env.ruflo` (gitignored)

- [ ] **Step 1: Add .env.ruflo to .gitignore**

Open `.gitignore` and confirm `.env.ruflo` is covered by the existing `.env` rule. If not, add it:

```
.env.ruflo
```

- [ ] **Step 2: Create telemetry env file**

Create `.env.ruflo`:

```bash
CLAUDE_CODE_ENABLE_TELEMETRY=1
CLAUDE_FLOW_TOOL_GROUPS=core,dev
CLAUDE_FLOW_HOOKS_ENABLED=true
```

- [ ] **Step 3: Set up telemetry tracking directory**

```bash
cd ruflo && source ../.env.ruflo && npx ruflo analysis setup-telemetry
```

Expected: creates `.claude-flow/metrics/` directory and confirms telemetry is active.

- [ ] **Step 4: Verify env vars are loaded**

```bash
echo $CLAUDE_CODE_ENABLE_TELEMETRY
```

Expected: `1`

---

### Task 4: Register ruflo as an MCP server

Ruflo exposes its tools to Claude Code via MCP. This makes the swarm commands available in-session.

**Files:**
- Modify: `.claude/settings.json`

- [ ] **Step 1: Read current .claude/settings.json**

```bash
cat .claude/settings.json
```

Note the current contents — you will add to `mcpServers`, not replace it.

- [ ] **Step 2: Add ruflo MCP server**

Edit `.claude/settings.json` to add the `claude-flow` MCP entry:

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    },
    "claude-flow": {
      "command": "npx",
      "args": ["claude-flow@alpha", "mcp", "start"],
      "cwd": "./ruflo"
    }
  }
}
```

- [ ] **Step 3: Restart Claude Code**

Quit and reopen Claude Code so the new MCP server loads.

- [ ] **Step 4: Verify MCP tools are available**

In a new Claude Code session run:

```
/mcp
```

Expected: `claude-flow` appears in the MCP tools list with tools like `mcp__claude-flow__token_usage`.

- [ ] **Step 5: Commit settings**

```bash
git add .claude/settings.json
git commit -m "feat: register ruflo as MCP server in Claude Code"
```

---

### Task 5: Run the hierarchical swarm planning session

This is the first swarm run. The queen agent receives the Tic-Tac-Toe brief, dispatches to specialists, and synthesises `docs/architecture.md`.

**Files:**
- Create: `docs/architecture.md`

- [ ] **Step 1: Confirm ANTHROPIC_API_KEY is set**

```bash
echo $ANTHROPIC_API_KEY | head -c 10
```

Expected: prints first 10 chars of your key (e.g. `sk-ant-api`). If empty, export it:

```bash
export ANTHROPIC_API_KEY=your_key_here
```

- [ ] **Step 2: Source telemetry env**

```bash
source .env.ruflo
```

- [ ] **Step 3: Launch the swarm planning session**

```bash
cd ruflo && npx ruflo swarm \
  --topology hierarchical \
  --agents queen,architect,backend,frontend,api-contract \
  --task "Plan the architecture for a Tic-Tac-Toe web app. Stack: Python 3.14 + FastAPI + SQLite backend, Next.js + 3D GPU-accelerated frontend, Docker + gRPC-Web transport. Output: system design overview, component breakdown, gRPC-Web proto definitions, SQLite schema, Next.js component tree." \
  --output ../docs/architecture.md \
  --config ../.ruflo/config.json
```

Expected: swarm initialises, queen decomposes task, 4 worker agents run in parallel, queen synthesises output to `docs/architecture.md`.

> **Note:** If the exact `--agents` flag syntax differs in the installed version, check `npx ruflo swarm --help` for the correct flags.

- [ ] **Step 4: Verify output exists and has content**

```bash
wc -l ../docs/architecture.md
```

Expected: more than 50 lines. If the file is empty or missing, check swarm logs in `ruflo/.claude-flow/metrics/`.

- [ ] **Step 5: Check cost**

```bash
npx ruflo analysis claude-cost
npx ruflo analysis token-usage
```

Review per-agent cost breakdown. Haiku agents (backend, frontend, api-contract) should cost ~15x less than the Sonnet agents (queen, architect).

- [ ] **Step 6: Commit the architecture document**

```bash
cd ..
git add docs/architecture.md
git commit -m "docs: add Tic-Tac-Toe architecture from ruflo swarm planning session"
```

---

### Task 6: Review and validate swarm output

**Files:**
- Read: `docs/architecture.md`

- [ ] **Step 1: Open and read docs/architecture.md**

Check it contains all required sections:
- [ ] System design overview
- [ ] Component breakdown
- [ ] gRPC-Web proto definitions
- [ ] SQLite schema
- [ ] Next.js component tree

- [ ] **Step 2: Flag any missing sections**

If a section is missing, re-run the swarm with a targeted task for that section only:

```bash
cd ruflo && npx ruflo swarm \
  --topology hierarchical \
  --agents queen,api-contract \
  --task "Define gRPC-Web proto definitions for Tic-Tac-Toe. Game state, moves, and player actions. Append to existing architecture doc." \
  --output ../docs/architecture.md \
  --config ../.ruflo/config.json
```

- [ ] **Step 3: Final commit if any fixes were made**

```bash
git add docs/architecture.md
git commit -m "docs: fill missing sections in architecture doc"
```

---

## Post-plan: next steps

Once `docs/architecture.md` is complete and committed:

1. Run `/brainstorming` on the architecture doc to validate it
2. Run `/writing-plans` to create the Tic-Tac-Toe implementation plan
3. Run `/subagent-driven-development` to execute the plan
