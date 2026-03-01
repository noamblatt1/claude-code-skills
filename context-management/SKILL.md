---
name: context-management
description: >
  Context management system for vibe coders working with AI coding agents (Claude Code, Cursor, etc.).
  Use when a user wants to set up or improve how they maintain project context across sessions with a coding agent.
  Triggers: "set up context management", "create CLAUDE.md", "create STATE.md", "manage context",
  "start a new project", "session management", "context files", or when the user is struggling with
  context loss between sessions. Also use when the user asks to update, review, or restructure their
  project's context files.
---

# Context Management for AI Coding Agents

A system for maintaining project context across sessions. Two files + on-demand docs.

## Core Philosophy

1. **Context is finite.** Every token competes for model attention. Write less, mean more.
2. **Pointers over content.** Never write in context what the agent can find by reading the codebase. Reference file paths, not summaries of what's inside them.
3. **User controls updates.** Never update context files unless the user asks. When asked, evaluate what actually changed.
4. **The agent is a CTO.** Treat it as a senior partner who takes ownership, not a tool that executes commands.

## System Architecture

```
project-root/
├── CLAUDE.md          # HOW to work (stable, ~100 lines)
├── STATE.md           # WHERE we are (volatile, ~50-80 lines)
└── docs/              # Deep context (on-demand)
    ├── CONTEXT-PROTOCOL.md
    ├── ARCHITECTURE.md
    ├── LESSONS.md
    └── [DOMAIN].md
```

**Always loaded:** CLAUDE.md + STATE.md (together < 150 lines)
**On-demand:** docs/ files, loaded only when working on that domain

## File Responsibilities

### CLAUDE.md — Instructions (stable, rarely changes)

Defines behavior, rules, and a pointer index to deeper docs.

Contains:
- Role + communication style (brief)
- Problem-solving rules
- Code standards
- Pointer index: a table mapping domains to file paths in docs/
- One-line pointer to the Context Update Protocol (see below)

Does NOT contain: current tasks, project status, code snippets, domain details, or the full update protocol.

### STATE.md — Current State (volatile, updated per session)

Always loaded. Tells the agent where the project stands.

Contains:
- 2-3 line project summary (stable anchor at top)
- Current task + acceptance criteria
- System status table (what's live, in progress, planned)
- Next 3 priorities
- Self-enforcing size rule at the top

Does NOT contain: behavioral instructions, architecture details, lessons learned.

**Critical:** STATE.md must include a size constraint that the agent sees every time:
`<!-- This file must stay under 80 lines. If it grows, prune or move content to docs/. -->`

### docs/ — Deep Context (on-demand)

Loaded only when working on a specific domain.

- **CONTEXT-PROTOCOL.md** — the context update protocol (see section below)
- **ARCHITECTURE.md** — tech stack, key decisions with rationale, key file paths
- **LESSONS.md** — only system-critical lessons (not every bug fix)
- **[DOMAIN].md** — one file per complex system (payments, scraper, auth, etc.)

## Context Update Protocol

The update protocol lives in its own file: `docs/CONTEXT-PROTOCOL.md`. It is NOT inside CLAUDE.md.

CLAUDE.md contains only a one-line pointer:
`When I ask "should we update context?" → read docs/CONTEXT-PROTOCOL.md and follow it.`

**Why separate?** Two reasons.

1. **Attention.** If the full protocol lives inside CLAUDE.md, the agent sees update instructions at the start of every session, taking up attention while the user is in the middle of building. By externalizing it, the agent only loads those instructions when actually needed: at session end.

2. **Precision.** The agent knows the protocol file exists (it's referenced in CLAUDE.md) and will use it when asked. But the protocol is short and focused. Injecting it at the right moment keeps the agent precise about how to evaluate and update, rather than "freestyling" updates based on vague memory of the rules.

The protocol is loaded on-demand only when the user asks "should we update context?" at session end. See `assets/CONTEXT-PROTOCOL.md` for the full protocol (to be placed at `docs/CONTEXT-PROTOCOL.md` in the project).

## What Makes a Good Pointer

Bad: `Payment → /src/payments/`
Good: `Payment (Stripe webhooks, LemonSqueezy checkout) → /src/payments/`

The extra few words save a file read to determine relevance.

## MEMORY.md — Agent's Notebook (Optional)

Claude Code has an auto-memory feature (MEMORY.md) that persists notes the agent accumulates during work: debugging gotchas, library quirks, patterns discovered.

**This is complementary to our system, not part of it.**

- CLAUDE.md + STATE.md = user-curated context (you control)
- MEMORY.md = agent-accumulated knowledge (agent controls)

**Rule:** Periodically review MEMORY.md. Move anything system-critical to docs/LESSONS.md. Let the rest stay as the agent's working notes.

Do not reference MEMORY.md in CLAUDE.md or STATE.md. It lives alongside them but operates independently.

## Session Workflow

1. Agent reads CLAUDE.md → sees pointer index + rules
2. Agent reads STATE.md → sees current task + project status
3. Agent loads relevant docs/ based on the task (if needed)
4. Work happens
5. When user asks "should we update context?" → agent reads `docs/CONTEXT-PROTOCOL.md` and follows it

## Setting Up a New Project

When the user asks to set up context management, **interview them first**. Do not generate files from assumptions.

1. Ask the user about: their role, technical level, communication preferences, project goals, current status, and any specific rules or standards.
2. Generate CLAUDE.md from their answers using the template in `assets/CLAUDE.template.md`.
3. Generate STATE.md from their answers using the template in `assets/STATE.template.md`.
4. Place `assets/CONTEXT-PROTOCOL.md` at `docs/CONTEXT-PROTOCOL.md`.
5. Create the docs/ folder. Add domain files only when a domain gets complex enough.
6. Present the generated files for user review and approval before saving.
