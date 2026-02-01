# Common Squad Workflows

Pre-configured agent squads for common use cases.

## Content Marketing Squad

**Goal:** Produce blog posts, articles, and SEO content.

**Agents:**
- Fury (Researcher) — Finds sources, data, quotes
- Vision (SEO Analyst) — Keywords, search intent, optimization
- Loki (Writer) — Writes the content
- Jarvis (Lead) — Coordinates, reports to human

**Setup:**
```bash
agent-squad init content-squad
agent-squad add fury --role "Researcher" --personality skeptical
agent-squad add vision --role "SEO Analyst" --personality analytical
agent-squad add loki --role "Content Writer" --personality creative
agent-squad add jarvis --role "Squad Lead"
agent-squad config --tasks linear --project "Content"
agent-squad start
```

**Workflow:**
1. Human creates Linear issue with topic/keywords
2. Jarvis assigns to Vision (SEO) → Fury (Research) → Loki (Write)
3. Vision posts SEO brief with target keywords
4. Fury researches, posts findings with sources
5. Loki writes draft, optimizes for SEO
6. Jarvis notifies human: "Draft ready for review"

**Task States:**
- Backlog → SEO Research → Research → Writing → Review → Done

---

## Social Media Squad

**Goal:** Create platform-specific content from source material.

**Agents:**
- Fury (Researcher) — Gathers source content, trends
- Quill (Social Manager) — Creates platform content
- Jarvis (Lead) — Schedules, reports metrics

**Setup:**
```bash
agent-squad init social-squad
agent-squad add fury --role "Content Researcher"
agent-squad add quill --role "Social Media Manager" --personality creative
agent-squad add jarvis --role "Squad Lead"
agent-squad config --tasks trello
agent-squad start
```

**Workflow:**
1. Human posts source (blog post, video, announcement)
2. Fury extracts key insights, quotable moments
3. Quill creates: Twitter thread, LinkedIn post, etc.
4. Jarvis queues for scheduling

---

## Product Development Squad

**Goal:** Research, design, and build product features.

**Agents:**
- Shuri (Product Analyst) — User research, competitive analysis
- Friday (Developer) — Implements features
- Fury (Researcher) — Technical research
- Jarvis (Lead) — Coordination

**Setup:**
```bash
agent-squad init product-squad
agent-squad add shuri --role "Product Analyst" --personality skeptical
agent-squad add friday --role "Developer"
agent-squad add fury --role "Technical Researcher"
agent-squad add jarvis --role "Squad Lead"
agent-squad config --tasks linear
agent-squad start
```

**Workflow:**
1. Feature request created
2. Shuri researches: user needs, competitive solutions
3. Fury researches: technical approaches, libraries
4. Friday implements with Shuri's requirements
5. Jarvis tracks progress, reports blockers

---

## Daily Standup Pattern

**Goal:** Daily progress summary to human.

**Add to Jarvis (Lead) config:**
```bash
# Additional cron for daily standup
openclaw cron add \
  --name "squad-standup" \
  --cron "0 9 * * *" \
  --session "isolated" \
  --message "Compile yesterday's activity from Linear. Send standup summary to human via Telegram. Include: completed tasks, in-progress, blockers."
```

**Standup Format:**
```
📊 DAILY STANDUP — [Date]

✅ COMPLETED
• Agent: Task completed

🔄 IN PROGRESS
• Agent: Current task

🚫 BLOCKED
• Agent: Blocker description

📋 UPCOMING
• Tasks ready to start
```

---

## Notification Pattern

**Goal:** Agents @mention each other for handoffs.

**In AGENTS.md:**
```markdown
## @Mention Rules

- @fury → When research needed
- @loki → When draft ready for writing
- @vision → When SEO review needed
- @jarvis → When human notification needed
- @all → When everyone should see
```

**How it works:**
1. Agent posts comment: "Research complete @loki"
2. Next time Loki wakes (heartbeat), sees @mention
3. Loki acts on the notification

---

## Research → Draft → Review Flow

**Standard content workflow:**

**Hour 0:** Task created
- Human: Create issue "Blog: [Topic]"
- Jarvis: Assigns to researcher

**Hour 0-1:** Research phase
- Fury heartbeat: Sees assignment
- Fury: Researches, posts findings
- Fury: "Research complete @loki @vision"
- Linear: Move to "Writing"

**Hour 1-2:** Writing phase
- Loki heartbeat: Sees @mention
- Loki: Reads research, writes draft
- Loki: Posts draft, @mentions jarvis
- Linear: Move to "Review"

**Hour 2-3:** Review phase
- Jarvis heartbeat: Sees @mention
- Jarvis: Reviews draft quality
- Jarvis: Telegram to human: "Draft ready: [link]"

**Hour 3+:** Human review
- Human: Edits/approves in GitHub/Linear
- Human: Comments "Approved, publish"
- Jarvis: Moves to "Done"

---

## Escalation Patterns

**When agents should escalate to human:**

| Scenario | Action |
|----------|--------|
| Missing critical info | @jarvis + comment "Need clarification on X" |
| Conflicting research | @jarvis + summarize conflict |
| Blocked >24 hours | @jarvis + blocker description |
| Quality concerns | @jarvis + specific issues |
| Scope creep | @jarvis + "Scope expanded, need guidance" |

---

## Multi-Project Squads

**One squad, multiple workstreams:**

Use Linear projects or Trello boards per workstream:
- Project: Blog Content
- Project: Social Media
- Project: Email Newsletter

Jarvis (lead) assigns across projects based on agent capacity.

---

## Seasonal/ Campaign Mode

**Temporary intensity increase:**

```bash
# Increase heartbeat frequency during campaign
agent-squad disable loki
agent-squad add loki --role "Writer" --schedule "*/5 * * * *"  # Every 5 min
agent-squad start

# After campaign, return to normal
agent-squad stop
agent-squad add loki --role "Writer" --schedule "*/15 * * * *"
agent-squad start
```

---

## Handoff Templates

**Research → Writer:**
```
Research complete for [topic].

Key findings:
- [Finding 1] ([source])
- [Finding 2] ([source])

Notes:
- [Special considerations]
- [Suggested angle]

@writer — ready for you.
```

**Writer → Lead:**
```
Draft complete: [link]

Word count: [X]
Key sections: [outline]
SEO notes: [optimization done]

@jarvis — ready for review.
```

**Lead → Human:**
```
📝 Deliverable Ready: [Title]

Type: [blog post/social/etc]
Word count: [X]
Sources: [X] cited
SEO optimized: [yes/no]

Review: [link]

Reply "approved" to publish, or comment with edits.
```
