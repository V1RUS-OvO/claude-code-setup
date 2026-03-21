# MCP Server Setup Guide

## Installation (Windows)

All MCPs are configured in `~/.claude/settings.json` under `mcpServers`.
Replace `C:\\Program Files\\nodejs\\npx.cmd` with your actual npx path.

```bash
# Find your npx path
where npx
```

---

## Active MCPs

### playwright — Browser Automation
```json
{
  "command": "npx.cmd",
  "args": ["@playwright/mcp@latest"]
}
```
Capabilities: navigate, click, fill forms, screenshot, PDF, network intercept
Best for: E2E testing, web scraping with JS rendering, UI automation

### context7 — Live Library Docs
```json
{
  "command": "npx.cmd",
  "args": ["-y", "@upstash/context7-mcp"]
}
```
Usage: add `use context7` to any prompt asking about a library
Example: `How do I use Prisma transactions? use context7`
Supports: React, Next.js, Vue, Prisma, Drizzle, FastAPI, Django, Tailwind, Vitest, tRPC, Zod, and 1000+ more

### memory — Cross-session Knowledge Graph
```json
{
  "command": "npx.cmd",
  "args": ["-y", "@modelcontextprotocol/server-memory"]
}
```
Stores entities and relations that persist across Claude sessions.
Use for: project facts, decisions, preferences that outlast MEMORY.md

### sequential-thinking — Reasoning Chains
```json
{
  "command": "npx.cmd",
  "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"]
}
```
Use for: complex multi-step problems, architectural decisions, debugging chains

### filesystem — Extended File Access
```json
{
  "command": "npx.cmd",
  "args": ["-y", "@modelcontextprotocol/server-filesystem", "C:\\Users\\KAKA"]
}
```
Allows read/write access to the specified path and all subdirectories.

### fetch — HTTP / Web Scraping
```json
{
  "command": "npx.cmd",
  "args": ["-y", "fetch-mcp"]
}
```
Fetches URLs and converts HTML to Markdown. Good for documentation pages.

### sqlite — Local Database
```json
{
  "command": "npx.cmd",
  "args": ["-y", "mcp-server-sqlite", "--db-path", "C:\\Users\\KAKA\\claude-memory.db"]
}
```
SQL queries against a local SQLite file. Good for structured persistent data.

### duckduckgo — Web Search
```json
{
  "command": "npx.cmd",
  "args": ["-y", "duckduckgo-mcp-server"]
}
```
**Use this instead of built-in WebSearch** (which only searches Cursor docs in this environment).

### github — GitHub API
```json
{
  "command": "npx.cmd",
  "args": ["-y", "@modelcontextprotocol/server-github"],
  "env": {
    "GITHUB_PERSONAL_ACCESS_TOKEN": "YOUR_PAT"
  }
}
```
Capabilities: search repos, read files, create/list issues, manage PRs, search code
Alternative: `curl -H "Authorization: token YOUR_PAT" https://api.github.com/...`

---

## Recommended Additional MCPs

| MCP | Package | Use case |
|-----|---------|----------|
| brave-search | `@modelcontextprotocol/server-brave-search` | Better search (needs API key) |
| postgres | `@modelcontextprotocol/server-postgres` | Query PostgreSQL databases |
| redis | `punkpeye/mcp-server-redis` | Redis operations |
| puppeteer | `@modelcontextprotocol/server-puppeteer` | Alternative browser automation |
| obsidian | `calclavia/mcp-obsidian` | If using Obsidian for notes |

Full list: see `memory/mcp-servers.md`
