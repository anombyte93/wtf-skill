# Phase: PRESENT — Lay It Out Plain

## The One Rule

**Every item gets one sentence. If you can't explain it in one sentence, you don't understand it yet.**

## Output Template

Use exactly this structure. Skip sections that have zero items. Don't add sections.

### Section 1: What Exists

Table format. Sort by "thing you type" first, then configs, then daemons/services.

```markdown
## What Exists

| Thing | What It Does | Where It Lives |
|-------|-------------|----------------|
| `cc`  | Launches Claude in tmux with max account | ~/.zshrc → shell-aliases.sh |
```

Rules:
- "What It Does" is ONE sentence, plain language, no jargon
- "Where It Lives" is the file path or "alias in ~/.zshrc" or "systemd service"
- Group by layer: commands first, then configs, then services/daemons
- Add a blank row between groups

### Section 2: How It Connects

ASCII diagram. Show layers and arrows for what-calls-what.

```markdown
## How It Connects

    You type "cc"
        → shell-aliases.sh::_cl_launch()
            → reads switcher-backends.json
            → creates tmux session
                → Claude boots → MCP server registers with mothership
```

Rules:
- One diagram per distinct workflow the user cares about
- Show the actual function/script names, not abstractions
- Arrow (→) means "calls" or "reads"
- Keep it under 20 lines per diagram

### Section 3: What's Broken / Redundant / Stale

```markdown
## What's Wrong

| Status | Thing | Issue | Fix |
|--------|-------|-------|-----|
| ✗ | `alias aq='ai tmux'` | Broken — `ai` has no tmux subcommand | Remove from .zshrc |
| ⚠ | `~/bin/c` | Redundant — does same thing as `cc` | Delete after verifying nothing depends on it |
| ◌ | `alias zai=...` | Stale — superseded by `czai()` | Remove from .zshrc |
```

Status icons:
- ✗ = broken (doesn't work)
- ⚠ = redundant (works but duplicates something else)
- ◌ = stale (outdated, superseded)
- ⚡ = will break if you change X (dependency warning)

### Section 4: Your Actual Workflow

For each common action the user performs, show the real path:

```markdown
## What Actually Happens

**Starting a new AI session:**
1. You type `cc` in terminal
2. `_cl_launch("claude-max")` reads `switcher-backends.json`
3. Bitwarden unlocks, secrets injected
4. tmux session "max-projectname" created
5. Claude CLI starts, loads MCP servers
6. You're working
```

Rules:
- Number every step
- Name the actual file/function at each step
- Only cover workflows relevant to the user's confusion

### Section 5: Also Found (added by completeness reviewer)

Only appears if the review agent found gaps:

```markdown
## Also Found

Things the first pass missed:
- [item and why it matters]
- [connected system that wasn't covered]
- [thing that will break if user acts on suggestions above]
```

## Evidence Gate

**Gate: All 4+ sections present. Every item has a one-liner. No jargon. User can read this and go "ok now I get it."**
