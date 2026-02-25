# Fleet Management (Optional)

Some cockpits manage a fleet of sub-cockpits. This is the pattern for that.

## When You Need This

You need fleet management when one person wears multiple hats, each with its own cockpit:

```
Mothership Cockpit (Director of AI)
├── Sub-cockpit: CISO
├── Sub-cockpit: Greenmark IT
├── Sub-cockpit: Software Engineer
└── Sub-cockpit: Investment Analyst
```

The mothership sees across all sub-cockpits but doesn't duplicate their work.

## Setting Up a Fleet Registry

Create a `roles/` directory in the mothership cockpit:

```
roles/
├── README.md           # Registry & metadata
├── role-one/
│   └── README.md       # Scope, goals, key people, links
├── role-two/
│   └── README.md
└── role-three/
    └── README.md
```

### Role README Template

```markdown
# <Role Name>

## Scope
What this hat covers. One paragraph.

## Strategic Goals
- Goal 1
- Goal 2

## Key People
- Name — role, relationship

## Dedicated Cockpit
- Repo: `org/cockpit-repo-name`
- Local: `~/repos/path/to/cockpit`

## Hand-off Notes
What someone taking over this role needs to know.
```

## Fleet-Aware cockpit-status

Add a `fleet` section to your `state.json` custom key:

```json
{
  "custom": {
    "fleet": [
      {
        "name": "CISO",
        "path": "~/repos-aic/ciso",
        "repo": "org/ciso-cockpit"
      },
      {
        "name": "Greenmark IT",
        "path": "~/repos-greenmark/greenmark-planning",
        "repo": "greenmark-waste-solutions/greenmark-planning"
      }
    ]
  }
}
```

`/cockpit-status` will detect the `fleet` key and show a fleet summary:

```
FLEET STATUS
| Cockpit       | Last Land      | State   | Branch |
|---------------|----------------|---------|--------|
| CISO          | 2h ago         | paused  | main   |
| Greenmark IT  | 1d ago         | done    | main   |
```

It does this by reading the latest bookmark for each fleet cockpit's path.

## Design Rules

1. **Mothership doesn't duplicate** — Sub-cockpit work stays in sub-cockpits.
2. **Registry is strategic** — Scope, goals, hand-off notes. Not task lists.
3. **Bookmarks are the glue** — Fleet status comes from bookmark files, not from scanning repos.
4. **Each cockpit is independent** — Sub-cockpits don't need to know about the mothership.
