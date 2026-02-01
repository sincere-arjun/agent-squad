# 🤖 Agent Squad

Multi-agent orchestration for OpenClaw. Create teams of specialized AI agents that collaborate on tasks via shared workspaces and heartbeat scheduling.

> *"One AI doing everything is a bottleneck. Ten specialized agents working together is a force multiplier."*

---

## What This Does

**Before:** One AI assistant handles all your tasks. Context gets bloated. Output is generic.

**After:** Specialized agents with distinct roles collaborate through a shared task system:
- **Fury** (Researcher) — Digs deep, finds sources, verifies facts
- **Loki** (Writer) — Crafts sharp, opinionated content  
- **Vision** (SEO) — Optimizes for search and intent
- **Jarvis** (Lead) — Coordinates, delegates, reports to you

Each agent wakes up every 15 minutes, checks for assigned work, collaborates via comments, then goes back to sleep.

---

## Quick Start

```bash
# Install globally
npm install -g agent-squad

# Create your squad
agent-squad init content-squad

# Add specialized agents
agent-squad add fury --role "Researcher" --personality skeptical
agent-squad add loki --role "Content Writer" --personality creative
agent-squad add jarvis --role "Squad Lead"

# Connect to your task system
agent-squad config --tasks linear --project "Content Squad"

# Start the heartbeats
agent-squad start
```

That's it. Your agents will now wake every 15 minutes, check Linear for tasks, and collaborate.

---

## How It Works

### The Heartbeat Pattern

Instead of always-on agents (expensive), agents wake on schedule:

```
:00  Jarvis checks Linear → delegates tasks
:05  Fury checks → researches, posts findings
:10  Loki checks → writes draft, @mentions Jarvis
```

Agents check for:
- Tasks assigned to them
- @mentions in comments
- Activity on threads they're subscribed to

If nothing to do → `HEARTBEAT_OK` and sleep until next cycle.

### Shared Task Systems

Agents coordinate via your existing tools:

| System | Use Case |
|--------|----------|
| **Linear** | Software teams, issue tracking |
| **Trello** | Visual boards, simpler workflows |
| **GitHub Issues** | Code projects, developer-focused |
| **Files** | Markdown in git repo, simple setup |

### Agent Personalities (SOUL.md)

Each agent has a `SOUL.md` file defining:
- **Role** — What they're responsible for
- **Personality** — How they approach work
- **Process** — Step-by-step how they work
- **Tools** — Which skills they use
- **Communication style** — How they write

Example:
```markdown
# SOUL.md — Who You Are

**Name:** Loki
**Role:** Content Writer

## Personality
Sharp, slightly cynical. Hates corporate speak. 
Every sentence must earn its place.

## What You're Good At
- Hooks that grab attention
- Cutting 20% of words without losing meaning
- Writing that sounds human

## What You Care About
- No "In today's world..." openings
- Oxford commas (pro), passive voice (anti)
- Specific examples over abstract advice
```

---

## Real Workflow Example

**You want a blog post on AI agent security.**

**Hour 0:** You create a Linear issue: "Blog: AI Agent Security"

**Hour 0:** Jarvis (lead) sees the issue, assigns to Fury (researcher)

**Hour 1:** Fury wakes up, researches:
- Finds primary sources on agent vulnerabilities
- Extracts statistics and expert quotes
- Posts findings: "Research complete @loki"

**Hour 2:** Loki wakes up, sees @mention:
- Reads Fury's research
- Writes 1,000 word draft
- Posts: "Draft ready @jarvis"

**Hour 3:** Jarvis wakes up, sees @mention:
- Reviews the draft
- Sends you a Telegram: "📝 Draft ready for review: [link]"

**You:** Edit in GitHub, approve with comment

**Jarvis:** Moves issue to "Done"

All while you were in meetings.

---

## Pre-Built Agent Personalities

### Fury — The Researcher
Obsessive fact-checker. Every claim needs a source. Finds primary sources, extracts data, spots misinformation.

### Loki — The Writer  
Sharp, cynical voice. Hates fluff. Pro-hooks, anti-passive-voice. Writes like a smart human, not a brand.

### Vision — The SEO Analyst
Thinks in keywords and search intent. Optimizes content before and after writing.

### Jarvis — The Squad Lead
Coordinator. Delegates, tracks progress, knows when to escalate. Your interface to the squad.

### Friday — The Developer
Clean code, tests, documentation. Precise and systematic.

### Quill — Social Media Manager
Hook-obsessed. Thinks in threads and viral potential. Platform-native content.

---

## Commands

```bash
# Squad management
agent-squad init <name>              # Create new squad
agent-squad status                   # Show all agents and health

# Agent management
agent-squad add <name> [options]     # Add agent
  --role "Description"
  --personality skeptical|creative|analytical|balanced
  --schedule "*/15 * * * *"
  --model "kimi-code"

agent-squad edit <name>              # Edit SOUL.md
agent-squad remove <name>            # Remove agent
agent-squad disable <name>           # Pause heartbeats
agent-squad enable <name>            # Resume heartbeats

# Task system
agent-squad config --tasks linear    # Use Linear
agent-squad config --tasks trello    # Use Trello
agent-squad config --tasks github    # Use GitHub Issues

# Operations
agent-squad start                    # Start all heartbeats
agent-squad stop                     # Stop all heartbeats
```

---

## Configuration

### Linear Integration

```bash
# Make sure linear skill is installed and configured
linear auth --token YOUR_TOKEN

# Configure squad to use Linear
agent-squad config --tasks linear --project "Content Squad"
```

### Trello Integration

```bash
# Make sure trello skill is installed
trello auth --api-key KEY --token TOKEN

# Configure squad
agent-squad config --tasks trello --board "Content Board"
```

---

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
            │ (Linear/etc)  │
            └───────────────┘
```

---

## Why This Approach?

### Specialized > General
An agent who's "good at everything" is mediocre at everything. An agent who's "the skeptical tester who finds edge cases" will actually find edge cases.

### Async > Real-time
Always-on agents burn API credits doing nothing. Heartbeats (wake → work → sleep) are 10x cheaper and work fine for most workflows.

### Shared Context > Isolated
When agents share a task board, they see the full context: who's working on what, what was decided, what blocks progress.

---

## Inspired By

This implementation is inspired by [Bhanu Teja's Mission Control](https://x.com/pbteja1998/status/2017662163540971756) — a 10-agent squad system built on OpenClaw.

---

## Requirements

- [OpenClaw](https://openclaw.ai) installed and running
- Node.js 18+
- Task system credentials (Linear, Trello, or GitHub)

---

## License

MIT

---

## Contributing

This is early software. PRs welcome:
- New agent personalities
- Additional task system integrations
- Workflow templates
- Bug fixes

Built with 💜 by Tony Stark & Orion for workeragi.
