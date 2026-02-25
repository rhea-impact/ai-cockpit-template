# MCP Integrations

Cockpits become powerful when connected to external services via MCP servers. This guide covers the pattern.

## Configuration

MCP servers are configured in `.mcp.json` at the cockpit root:

```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "@org/mcp-server-name"],
      "env": {
        "API_KEY": "..."
      }
    }
  }
}
```

Enable servers in `.claude/settings.local.json`:

```json
{
  "enableAllProjectMcpServers": true
}
```

## Common Integration Patterns

### Email-Driven Cockpit
```json
{
  "mcpServers": {
    "outlook": { "...": "Outlook email automation" },
    "claude-in-chrome": { "...": "Browser automation for web apps" }
  }
}
```
Skills: `/daily-brief`, `/fetch-email`, `/cross-reference`, `/inbox-triage`

### Project Management Cockpit
```json
{
  "mcpServers": {
    "github": { "...": "GitHub issues, PRs, projects" },
    "wrike": { "...": "Wrike task management" },
    "backlog": { "...": "Backlog.md local task tracking" }
  }
}
```
Skills: `/task-out`, `/weekly-update`, `/cockpit-status`

### Data Engineering Cockpit
```json
{
  "mcpServers": {
    "supabase": { "...": "Database queries and management" },
    "github": { "...": "Code repos and CI/CD" }
  }
}
```
Skills: `/vendor-research`, `/pipeline-status`, `/data-quality`

## Security Rules

1. **Never commit secrets** — Use environment variables or a secrets manager (Knox, 1Password CLI, etc.)
2. **Read-only by default** — Start with read-only API keys. Add write access only when needed and proven safe.
3. **One server per concern** — Don't overload a single MCP server with unrelated capabilities.
4. **`.mcp.json` can be gitignored** — If it contains env vars that differ per machine, add it to `.gitignore` and document the expected shape in CLAUDE.md.

## Template `.mcp.json`

For cockpits that want to ship a starter configuration:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": ""
      }
    }
  }
}
```

Note the empty string for the token — this signals "you need to fill this in" without leaking a real secret.
