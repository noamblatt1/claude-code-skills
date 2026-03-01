# Context Management — Quick Guide for Humans

## What This System Does

Keeps your AI coding agent oriented across sessions. Two small files at project root give the agent everything it needs to pick up where you left off.

---

## The Files

### CLAUDE.md — "How to work with me"
**What:** Your agent's instructions. Role, rules, coding standards, and a pointer index to deeper docs.
**When to edit:** Rarely. Only when your working style or project rules change.
**Size:** ~100 lines max.

### STATE.md — "Where we are right now"
**What:** Current task, project status, next priorities.
**When to edit:** Every session (when you ask the agent to update it).
**Size:** ~50-80 lines max. There's a built-in reminder at the top to keep it lean.

### docs/CONTEXT-PROTOCOL.md — "How to update context"
**What:** The rules the agent follows when you ask "should we update context?" at session end.
**Why separate:** Keeping it outside CLAUDE.md means the agent only loads update logic when it's actually needed, not during the 99% of the session where you're building. This keeps the agent focused and precise.
**When to edit:** Rarely. The protocol is generic and works across projects.

### MEMORY.md — "Agent's notebook" (optional)
**What:** Claude Code's auto-memory. Notes the agent accumulates on its own: debugging gotchas, library quirks, patterns it discovered while working.
**Who controls it:** The agent. This is NOT part of your curated context.
**Your job:** Review it periodically. If something is system-critical, move it to docs/LESSONS.md. Let the rest stay.

### docs/ — "Deep dives"
**What:** Detailed context per domain. Architecture decisions, lessons learned, complex system docs.
**When to create:** Only when a domain gets complex enough to need its own file.
**Size:** No limit. These load on-demand, not every session.

---

## How to Run a Session

### Starting
1. Open your agent (Claude Code, Cursor, etc.)
2. The agent reads CLAUDE.md and STATE.md automatically
3. Tell it what you want to work on
4. It loads any relevant docs/ files if needed

### During work
Just work normally. The agent has the context it needs.

### Ending
Ask: **"Should we update context?"**

The agent reads `docs/CONTEXT-PROTOCOL.md` and checks:
- Did the current task change?
- Did a feature ship or break?
- Was an architectural decision made?
- Was a critical lesson learned?
- Is anything in STATE.md now outdated?

If yes to any → it suggests specific edits. You approve or adjust.
If no → you're done.

---

## Golden Rules

1. **You control updates.** The agent never modifies context files without you asking.
2. **Pointers, not summaries.** Context files reference file paths. They don't explain what's in those files. The agent can read them itself.
3. **Prune aggressively.** If STATE.md is growing past 80 lines, move stuff to docs/ or delete it.
4. **One file per domain.** When a system gets complex (payments, auth, scraping), give it a docs/ file.
5. **Decisions, not descriptions.** Write "We chose Stripe because X", not "Stripe is a payment processor that..."

---

## Setting Up a New Project

Ask the agent to set up context management. It will interview you and generate the files.

**In Claude Code:**
```
@path/to/context-management.skill — set up context management for this project.
Interview me about my preferences, the project, and current status,
then create the context files.
```

**In other agents:**
Drop the template files in your project root and ask:
```
Read the template files in this project root. Interview me about my
preferences and project details, then fill them in properly.
```

The agent asks you questions, generates the files, and you review and approve. One setup per project. The protocol inside the files keeps the system alive from that point forward.

---

## Common Mistakes to Avoid

| Mistake | Fix |
|---------|-----|
| Pasting code into context files | Use file paths instead |
| Writing long explanations | Bullet points and pointers |
| Updating context every 5 minutes | Only at session end, when you ask |
| Keeping all history | Prune. Only current state matters |
| One giant file for everything | Split: instructions / state / deep docs |
| Summarizing what the agent can grep | Delete it, add a pointer |
