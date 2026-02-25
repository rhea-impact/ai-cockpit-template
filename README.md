# AI Cockpit Template

Universal session lifecycle primitives for Claude Code workspaces.

**Hop in. Fly. Land.**

## What This Is

A cockpit is a Claude Code workspace with a rhythm:

1. **`/takeoff`** — Boot sequence. Reads your last bookmark, checks what changed, shows priorities. Waits for orders.
2. **Work** — Use your domain-specific skills. The cockpit tracks state.
3. **`/land`** — Park sequence. Captures what got done, what's next, writes a bookmark for next session.

This template provides the universal primitives that every cockpit needs. Fork it, add your domain-specific skills, and you have a cockpit.

## Quick Start

```bash
# Clone the template
gh repo create my-cockpit --template rhea-impact/ai-cockpit-template --public
cd my-cockpit

# Customize CLAUDE.md with your role context
# Add domain-specific skills to .claude/skills/

# Start working
claude
# > /takeoff
# > ... do your work ...
# > /land
```

## What's Included

### Skills (Universal Primitives)

| Skill | Trigger | What It Does |
|-------|---------|-------------|
| `/takeoff` | Start of session | Read bookmark, detect drift, show priorities, wait for orders |
| `/land` | End of session | Capture outcomes, blockers, next actions, write bookmark |
| `/cockpit-status` | Anytime | Show active workstreams, blockers, ages, who owes what |

### State Management

- **`state.json`** — Watermarks, counters, last-run timestamps. Skills read/write this to enable incremental operations.
- **Bookmarks** — Written to `~/.claude/bookmarks/` by `/land`. Read by `/takeoff` on next session. This is the bridge between sessions.

## Cockpit Architecture

```
your-cockpit/
├── .claude/
│   └── skills/
│       ├── takeoff/              # Boot sequence (from template)
│       ├── land/                 # Park sequence (from template)
│       ├── cockpit-status/       # Instrument panel (from template)
│       └── your-domain-skill/    # Your additions
├── state.json                    # Session state & watermarks
├── CLAUDE.md                     # Role context + instructions
└── ...                           # Your domain-specific structure
```

## Design Principles

1. **Cheap boots** — `/takeoff` reads 3 things max: CLAUDE.md (already loaded), state.json, latest bookmark. No API calls. No slow scans.
2. **Session contracts** — Every session has a lifecycle: boot → work → land. Bookmarks are the contract between sessions.
3. **Drift detection** — If the branch changed, files were modified, or commits landed since your last bookmark, `/takeoff` tells you.
4. **Domain-agnostic** — The template knows nothing about your project. It only knows about sessions, bookmarks, and state.
5. **Composable** — Add as many domain skills as you want. The primitives stay the same.

## Customization

See [docs/customization.md](docs/customization.md) for how to:
- Add domain-specific skills
- Extend `state.json` with custom watermarks
- Configure the cockpit status dashboard
- Set up project-specific takeoff checklists

## In the Wild

Cockpits built from this template:
- **AIC Director of AI** — Email-centric command post across 4 sub-roles
- **Greenmark Planning** — Waste management leadership planning hub
- *(Add yours here)*

## License

MIT. Built by [Rhea Impact](https://rheaimpact.com) — free software for coding agents.
