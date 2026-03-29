# mastering-ai-tools

Boilerplate for mastering AI-assisted development — Cursor AI, Claude Code, MCP servers, agent skills, and swarm orchestration.

---

## Skills

Skills are reusable agent behaviors stored in `.agents/skills/` (single source of truth) and symlinked into `.claude/skills/` for Claude Code and `.cursor/` for Cursor.

| Skill | When to use |
|---|---|
| `using-superpowers` | Start of every conversation — discovers and activates available skills |
| `brainstorming` | Before any creative work — explore intent and design before implementation |
| `writing-plans` | When you have a spec — create a detailed implementation plan before touching code |
| `executing-plans` | Execute a written plan in a separate session with review checkpoints |
| `subagent-driven-development` | Execute plans with independent tasks in parallel within the current session |
| `dispatching-parallel-agents` | 2+ independent tasks with no shared state or sequential dependencies |
| `test-driven-development` | Before writing any feature or bugfix implementation code |
| `systematic-debugging` | Before proposing fixes for any bug, test failure, or unexpected behavior |
| `architecture-patterns` | Clean Architecture, Hexagonal Architecture, DDD for new services or refactors |
| `python-design-patterns` | KISS, SRP, composition over inheritance — when designing or reviewing Python code |
| `requesting-code-review` | After completing tasks or before merging |
| `receiving-code-review` | Before implementing review feedback — verify, don't blindly apply |
| `using-git-worktrees` | Feature work needing isolation from the current workspace |
| `finishing-a-development-branch` | When implementation is done — choose merge, PR, or cleanup |
| `verification-before-completion` | Before claiming work is done — run commands, confirm output |
| `writing-skills` | Creating or editing skills before deployment |

### Setting up skills for Claude Code

Skills in `.claude/skills/` are auto-discovered by Claude Code. Most are symlinks to `.agents/skills/`:

```
.agents/skills/         ← source of truth
.claude/skills/         ← symlinks (auto-discovered by Claude Code)
```

No extra config needed — Claude Code picks them up on startup.

### Setting up skills for Cursor

Add a reference to `.agents/rules/` in your Cursor settings (`~/.cursor/mcp.json` or project `.cursor/rules`). Skills are invoked via agent prompts referencing the SKILL.md files.

---

## MCP Servers

MCP (Model Context Protocol) servers extend Claude Code and Cursor with external tool access.

### Available MCPs

| MCP | Purpose |
|---|---|
| `context7` | Up-to-date library and framework docs — resolves outdated training data |

### Setting up MCPs for Claude Code

Add to `.claude/settings.json` (project-level) or `~/.claude/settings.json` (global):

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    }
  }
}
```

Restart Claude Code after editing settings.

### Setting up MCPs for Cursor

Add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    }
  }
}
```

Enable in Cursor: **Settings → Features → MCP Servers → toggle on**.

---

## Stack

- **Backend**: Python 3.14 + FastAPI + SQLite
- **Frontend**: Next.js + 3D GPU-accelerated rendering
- **Transport**: Docker + gRPC-Web
- **Initial app**: Tic-Tac-Toe (learning/demo)
