---
name: agent-squad
description: Create and manage teams of specialized AI agents that work together on tasks. Use when setting up multi-agent workflows, agent squads for content/marketing/research pipelines, configuring heartbeat-based agent coordination, or managing agent personalities via SOUL.md files. Enables orchestration of multiple OpenClaw sessions as a coordinated team with shared task systems (Linear, Trello, etc.).
---

# Agent Squad

Orchestrate teams of specialized AI agents that collaborate on tasks via shared workspaces and heartbeat scheduling.

## Quick Start

```bash
# Initialize your squad
agent-squad init my-squad

# Add agents
agent-squad add researcher --role "Deep researcher. Finds sources and data."
agent-squad add writer --role "Content writer. Sharp, opinionated voice."
agent-squad add lead --role "Squad lead. Coordinates and reports."

# Configure task system
agent-squad config --tasks linear --project "Content Squad"

# Start heartbeats (agents wake every 15 min)
agent-squad start
```

## What This Enables

**Before:** One AI doing everything. Context bloat. Generic output.

**After:** Specialized agents with distinct roles, shared task board, coordinated via heartbeats. Each agent:
- Has a unique personality (SOUL.md)
- Checks for work every 15 minutes (cron)
- Posts updates to shared task system
- @mentions other agents when handoffs needed

## Core Concepts

### Agents = OpenClaw Sessions

Each agent is an isolated OpenClaw session with:
- Unique session key (`agent:{name}:main`)
- Custom `SOUL.md` (personality, role, tools)
- Heartbeat cron (wakes periodically)
- Access to shared task system

### The Heartbeat Pattern

Instead of always-on agents (expensive), agents wake on schedule:

```
:00  Lead checks Linear → delegates tasks
:05  Researcher checks → does research, @mentions Writer  
:10  Writer checks → reads research, writes draft
```

Agents check for @mentions, assigned tasks, and activity. If nothing to do → `HEARTBEAT_OK` and sleep.

### Shared Task Systems

Agents coordinate via task system comments:
- **Linear** (recommended) — Issues, comments, states
- **Trello** — Cards, lists, checklists
- **GitHub Issues** — Native integration
- **Files** — Markdown in git repo

## Commands

### Squad Management

```bash
agent-squad init <name>              # Create new squad workspace
agent-squad status                   # Show all agents and health
agent-squad logs <agent>             # View recent heartbeats
```

### Agent Management

```bash
agent-squad add <name> [options]
  --role "Description of role"
  --personality "skeptical|creative|analytical"
  --schedule "*/15 * * * *"
  --model "kimi-code"

agent-squad edit <name>              # Edit SOUL.md
agent-squad remove <name>            # Remove agent and cron
agent-squad disable <name>           # Pause heartbeats
agent-squad enable <name>            # Resume heartbeats
```

### Task System Integration

```bash
agent-squad config --tasks linear    # Use Linear
agent-squad config --tasks trello    # Use Trello
agent-squad config --tasks github    # Use GitHub Issues
agent-squad config --tasks file      # Use local markdown files
```

### Operations

```bash
agent-squad start                    # Start all heartbeats
agent-squad stop                     # Stop all heartbeats
agent-squad trigger <agent>          # Manual heartbeat trigger
agent-squad notify "@writer draft ready"  # Send notification
```

## Example: Content Marketing Squad

**3 agents, Linear integration:**

```bash
# Setup
agent-squad init content-squad
agent-squad add fury --role "Researcher. Every claim needs a source."
agent-squad add loki --role "Writer. Sharp, anti-fluff voice."
agent-squad add jarvis --role "Lead. Coordinates, reports to human."

agent-squad config --tasks linear --project "Content Squad"
agent-squad start
```

**Workflow:**
1. You create Linear issue: "Blog post: AI Agent Security"
2. Jarvis (lead) assigns to Fury (researcher)
3. Fury heartbeat → researches → posts findings → @mentions Loki
4. Loki heartbeat → writes draft → posts → @mentions Jarvis
5. Jarvis notifies you via Telegram: "Draft ready for review"

## SOUL.md Templates

See [references/personalities.md](references/personalities.md) for pre-built agent personalities:
- Researcher (Fury)
- Writer (Loki)
- Lead (Jarvis)
- SEO Analyst (Vision)
- Social Media (Quill)
- Developer (Friday)

## Advanced

### Custom Heartbeat Schedules

Stagger agents to prevent all waking at once:

```bash
agent-squad add lead --schedule "0,15,30,45 * * * *"
agent-squad add researcher --schedule "5,20,35,50 * * * *"
agent-squad add writer --schedule "10,25,40,55 * * * *"
```

### AGENTS.md (Shared Operations Manual)

Each agent workspace includes AGENTS.md with:
- Squad mission
- Communication rules (@mentions, handoffs)
- File conventions
- Escalation procedures

See [references/agents-template.md](references/agents-template.md)

### Multi-Model Squads

Use cheaper models for heartbeats, premium for creative work:

```bash
agent-squad add researcher --model "kimi-code"        # Cheap, fast
agent-squad add writer --model "claude-opus-4-5"      # Premium creative
```

## Architecture

```
┌─────────────────────────────────────────────┐
│           OpenClaw Gateway                  │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐       │
│  │  Lead   │ │Researcher│ │ Writer │       │
│  │ Session │ │ Session  │ │Session │       │
│  │ (Jarvis)│ │ (Fury)   │ │(Loki)  │       │
│  └────┬────┘ └────┬────┘ └────┬────┘       │
│       │           │           │             │
│       └───────────┼───────────┘             │
│                   │                         │
│              ┌────┴────┐                    │
│              │  Cron   │                    │
│              │ Jobs    │                    │
│              └────┬────┘                    │
└───────────────────┼─────────────────────────┘
                    │
            ┌───────┴───────┐
            │  Task System  │
            │  (Linear/etc) │
            └───────────────┘
```

## Requirements

- OpenClaw gateway running
- Task system credentials (Linear API key, Trello token, etc.)
- Node.js (for CLI)

## See Also

- [references/personalities.md](references/personalities.md) — Agent personality templates
- [references/agents-template.md](references/agents-template.md) — AGENTS.md template
- [references/workflows.md](references/workflows.md) — Common squad workflows
