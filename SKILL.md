# /wtf — "What the fuck is going on?"

You are overwhelmed. The user typed /wtf because they've lost track of their own systems. Your job is to find everything, lay it out plain, and make sure you didn't miss anything.

## The One Rule

**Find everything connected to what's confusing the user, then explain it like you're drawing on a napkin. Plain language. No jargon. One-liners.**

## How This Works

Two passes. No modes. No flags. No args needed.

1. **First pass:** Scan everything, lay it out plain (phases/SCAN.md → phases/PRESENT.md)
2. **Second pass:** A review agent re-reads every user message in this conversation, checks your output against what they actually asked about, and catches what you missed (agents/completeness-reviewer.md)

## Task Tracking

Create tasks at the start so the user can see progress. Mark each step in_progress when starting, completed when done.

```
TaskCreate: "/wtf Step 1: Infer confusion context"
TaskCreate: "/wtf Step 2: Triage (fast, no agents)"
TaskCreate: "/wtf Step 3: Deep scan tangled domains"
TaskCreate: "/wtf Step 4: Present findings (4-section output)"
TaskCreate: "/wtf Step 5: Completeness review"
```

Chain them: each blocked by the previous. Update status as you go. If the user already has pending tasks unrelated to /wtf, consolidate stale/unstarted ones into a single follow-up task that comes AFTER /wtf completes.

## Execution

### Step 1: Figure out what they're confused about
**TaskUpdate: Step 1 → in_progress**

Read the conversation. What were they doing? What broke? What spiraled? Don't ask — infer from context. If truly ambiguous, ask ONE question max.

Check these for context clues:
- Current working directory
- Recent conversation messages
- CLAUDE.md / MEMORY.md
- Running processes, tmux sessions

**TaskUpdate: Step 1 → completed**

### Step 2: Triage (10 seconds, no agents)
**TaskUpdate: Step 2 → in_progress**

Before any deep scanning, do fast cheap checks to figure out scope:

```bash
# What's in play right now?
pwd; ls
tmux list-sessions 2>/dev/null | head -10
ps aux | grep -E 'claude|node|python' | grep -v grep | head -10
```

Plus: grep for keywords from the user's confusion in aliases, ~/bin, configs. This tells you which domains are tangled and need deep scanning vs which are clean.

**TaskUpdate: Step 2 → completed**

### Step 3: Deep scan (load phases/SCAN.md)
**TaskUpdate: Step 3 → in_progress**

Dispatch the system-scanner Explore agent for tangled domains only. Don't scan what's clean.

**TaskUpdate: Step 3 → completed** (when agent returns)

### Step 4: Present (load phases/PRESENT.md)
**TaskUpdate: Step 4 → in_progress**

Fixed 4-section output. Every item gets a one-liner. No fluff.

**TaskUpdate: Step 4 → completed**

### Step 5: Completeness review (load agents/completeness-reviewer.md)
**TaskUpdate: Step 5 → in_progress**

Dispatch completeness-reviewer agent (model=sonnet, NOT haiku — haiku is too shallow). It catches:
- Things the user mentioned that you didn't cover
- Connected systems you missed
- Assumptions you made that need calling out
- Things that will break if the user acts on your suggestions

Integrate its findings into your response. Add a "Also Found" section if it caught things.

**TaskUpdate: Step 5 → completed**

## Output Format

Always these 4 sections (+ optional 5th from review):

```
## 1. What Exists
Table: thing | what it does (one sentence) | where it lives

## 2. How It Connects
ASCII diagram showing layers and what-calls-what

## 3. What's Broken / Redundant / Stale
Table with status icon:
  ✗ broken — what's wrong, how to fix
  ⚠ redundant — what it overlaps with
  ◌ stale — why it's outdated

## 4. Your Actual Workflow
"Here's what happens when you type X..."
Numbered steps, source to destination, for each common action

## 5. Also Found (from completeness review)
Things the review agent caught that the first pass missed
```

## Critical Reminder

You are read-only. Map the chaos. Explain it plain. Catch what you missed. That's it. The user decides what to change — you just give them visibility.

**Find everything connected. Explain it plain. Double-check you missed nothing.**
