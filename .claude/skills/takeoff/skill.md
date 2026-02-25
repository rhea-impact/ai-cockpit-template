# /takeoff — Cockpit Boot Sequence

## When to Use
Start of every session. This is the first thing you run.

## What It Does
Full situational awareness briefing: where we were, where we are, where we're going, and what's blocking us. No questions asked — show the state and wait for orders.

## Execution Steps

### 1. Gather Core State (cheap, in main context)

**A. state.json** — from the cockpit root
- Get `cockpit.name` (for the ASCII art header)
- Get `watermarks.last_land` (when was the last session?)
- Get `counters.sessions` (how many sessions total?)
- If `last_land` is null, this is the first session ever

**B. Latest bookmark** — scan `~/.claude/bookmarks/` for the most recently modified `*-bookmark.json` whose `project.path` matches the current working directory
- If found: read it for `lifecycle_state`, `context.summary`, `next_actions`, `blockers`, `confidence`
- If not found: note "No previous bookmark for this cockpit"

**C. Git state** — use the `gitStatus` block injected at session start
- Current branch, dirty files, recent commits are **already in context**
- **Do NOT re-run** `git status`, `git branch`, or `git log` — they're already there

### 2. Output ASCII Art Header

**Line 1: Cockpit name** — Generate the `cockpit.name` from state.json as bold Unicode block letters (same style as TAKEOFF below). This is dynamic per cockpit.

**Line 2: TAKEOFF** — Always this exact text:

```
  ████████╗ █████╗ ██╗  ██╗███████╗ ██████╗ ███████╗███████╗
  ╚══██╔══╝██╔══██╗██║ ██╔╝██╔════╝██╔═══██╗██╔════╝██╔════╝
     ██║   ███████║█████╔╝ █████╗  ██║   ██║█████╗  █████╗
     ██║   ██╔══██║██╔═██╗ ██╔══╝  ██║   ██║██╔══╝  ██╔══╝
     ██║   ██║  ██║██║  ██╗███████╗╚██████╔╝██║     ██║
     ╚═╝   ╚═╝  ╚═╝╚═╝  ╚═╝╚══════╝ ╚═════╝ ╚═╝     ╚═╝
```

**Block letter reference** — use this character set for generating the cockpit name:
```
█ ═ ╗ ╔ ╚ ╝ ║ ╠ ╣ ╦ ╩ ╬ ▀ ▄
```
Match the exact same visual weight and style as the TAKEOFF text above. Each letter should be 6-7 chars wide and 6 lines tall.

**Then the status bar:**
```
  BRANCH    <branch>          DIRTY  <yes (N) | clean>
  SESSION   #<N+1>            LAST   <time since last land>
```

If drift was detected (compare git state to bookmark's `workspace_state`):
```
  DRIFT     <branch changed / N new commits / N files modified outside session>
```

### 3. Launch Situational Awareness Subagent

Use the **Task tool** with `subagent_type: "Explore"` to scan the cockpit workspace and produce the four-part briefing. This keeps the heavy scanning out of the main context.

**Subagent prompt** (adapt the paths to the current cockpit):

```
Scan the cockpit workspace at <current working directory> and produce a situational awareness briefing. Read whatever exists — skip what doesn't.

Scan these (only if they exist):
- backlog/tasks/ — count tasks by status (to do, in progress, done, blocked)
- projects/ — list active projects, count checklist progress
- tasks/README.md — scan for waiting-on items and ages
- CLAUDE.md — pull any "Active Research", "Current State", "What's Active", "What's Blocked" sections
- state.json custom fields — domain-specific state

Also incorporate this bookmark context:
- Last session summary: <bookmark context.summary>
- Next actions from last session: <bookmark next_actions>
- Blockers from last session: <bookmark blockers>
- Lifecycle state: <bookmark lifecycle_state>

Output EXACTLY this format (keep each section to 1-5 lines, be concise):

WHERE WE WERE
  <what the last session accomplished, or "First session" if new>

WHERE WE ARE
  <current project state: active workstreams, task counts, domain-specific state>

WHERE WE'RE GOING
  1. <top priority>
  2. <second priority>
  3. <third priority>

BLOCKERS
  <blocked items with who/what and age, or "None">
```

**Output the subagent's result directly below the ASCII art header.**

### 4. Update State

Write to `state.json`:
- Set `watermarks.last_takeoff` to current ISO timestamp
- Increment `counters.sessions` by 1
- Increment `counters.takeoffs` by 1

### 5. STOP

Do NOT proceed with any work. Output:

```
Ready for orders.
```

And wait for the user to tell you what to do.

## Rules
- Reuse gitStatus from session context. Never re-fetch what's already there.
- The subagent does the heavy scanning. Main context stays lean.
- Never ask questions during takeoff. Just show state.
- If the previous session was `lifecycle_state: blocked`, make sure the subagent highlights blockers prominently.
- Keep each briefing section to 1-5 lines. Concise, not verbose.
- If the cockpit is bare (no backlog, no projects, no tasks), the subagent returns a minimal briefing: "Fresh cockpit. No projects or tasks tracked yet."
- If no bookmark exists and no state.json exists, this is a fresh cockpit — say "First flight. Cockpit initialized." and create state.json.
- If bookmark file is corrupted or unreadable, note "Bookmark corrupted — starting fresh" and continue without it.
