# Customizing Your Cockpit

## Adding Domain Skills

Create a new folder under `.claude/skills/` with a `skill.md` file:

```
.claude/skills/
├── takeoff/           # From template (don't modify)
├── land/              # From template (don't modify)
├── cockpit-status/    # From template (don't modify)
└── my-skill/          # Your addition
    └── skill.md
```

Your skill.md should follow this structure:

```markdown
# /my-skill — Short Description

## When to Use
One sentence: when should this skill be triggered?

## What It Does
One paragraph: what does it produce?

## Execution Steps
### 1. Step Name
What to do, what to read, what to write.

### 2. Step Name
...

## Rules
- Constraints and guardrails
```

## Extending state.json

Add domain-specific watermarks and counters under the `custom` key:

```json
{
  "cockpit": { ... },
  "watermarks": { ... },
  "counters": { ... },
  "custom": {
    "last_email_scan": "2026-02-25T12:00:00Z",
    "vendors_researched": 6,
    "meetings_processed": 12
  }
}
```

The template skills only read/write the top-level keys. Your domain skills own the `custom` key.

## Configuring cockpit-status Sections

Add a `status_sections` array to state.json to extend the dashboard:

```json
{
  "custom": {
    "status_sections": [
      {
        "name": "Vendor Status",
        "source": "reference/vendor-status.md",
        "type": "table"
      },
      {
        "name": "Credential Requests",
        "source": "tasks/README.md",
        "filter": "Waiting on"
      }
    ]
  }
}
```

`/cockpit-status` will include these sections at the bottom of the dashboard.

## CLAUDE.md Customization

The template CLAUDE.md has placeholder sections marked with `<!-- REPLACE -->` comments. Fill in:

1. **Role Context** — Who you are, what org, what mission
2. **Domain Skills** — Table of your added skills
3. **Project Structure** — Your directory layout
4. **Rules** — Domain-specific constraints

Keep the Cockpit Primitives and Session Protocol sections unchanged — they document the universal contract.

## Examples

### Meeting-Driven Cockpit (e.g., Greenmark Planning)

```
.claude/skills/
├── takeoff/
├── land/
├── cockpit-status/
├── diarize/            # Process meeting transcripts
├── task-out/           # Route action items to projects
├── weekly-update/      # Generate weekly intelligence reports
└── take-notes/         # Capture clipboard notes
```

### Email-Driven Cockpit (e.g., AIC Director)

```
.claude/skills/
├── takeoff/
├── land/
├── cockpit-status/
├── daily-brief/        # Scan inbox, download new emails
├── cross-reference/    # Find connections between emails
├── project-status/     # Aggregate project state from emails
└── fetch-email/        # Download specific email threads
```

### Code-Driven Cockpit (e.g., Software Engineer)

```
.claude/skills/
├── takeoff/
├── land/
├── cockpit-status/
├── feature-dev/        # Guided feature development
├── code-review/        # Review pull requests
└── deploy/             # Ship to production
```

## Updating Template Skills

When the template repo releases updates to the core skills, you can pull them in:

```bash
# Add template as upstream remote (one-time)
git remote add template https://github.com/rhea-impact/ai-cockpit-template.git

# Pull updates to core skills
git fetch template
git checkout template/main -- .claude/skills/takeoff .claude/skills/land .claude/skills/cockpit-status
git commit -m "Update cockpit primitives from template"
```

This only touches the three template skills. Your domain skills are untouched.
