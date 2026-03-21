# Agent Briefing - KAKA Workstation

> Quick onboarding guide for new agent models. Read this first.

---

## 1. Environment

| Item | Value |
|------|-------|
| OS | Windows 11 Pro (Build 22631) |
| Shell | Git Bash (MINGW64) |
| Node.js | v24.14.0 |
| npm | 11.9.0 |
| Python | 3.11.9 |
| Git | 2.50.1 |
| Docker | 28.3.3 |
| VS Code | Installed |
| GitHub CLI | Not installed |

---

## 2. Claude Code Config

| Item | Value |
|------|-------|
| Model | `claude-opus-4-6` |
| API Proxy | `https://uniquefox.top` |
| Config file | `C:/Users/KAKA/.claude/settings.json` |
| Extended Thinking | Enabled (`alwaysThinkingEnabled: true`) |
| Reply language | **Chinese (Simplified)** |

---

## 3. MCP Servers

| Name | Capability | Primary Use |
|------|-----------|-------------|
| `playwright` | Browser automation | Web interaction, screenshots, E2E tests |
| `context7` | Live library docs | Latest API docs for any library |
| `memory` | Persistent memory | Cross-session knowledge entities |
| `sequential-thinking` | Reasoning chains | Multi-step problem decomposition |
| `filesystem` | File system access | Read/write files under `C:/Users/KAKA` |
| `fetch` | HTTP fetch | Scrape web pages / call APIs |
| `sqlite` | Local database | Persistent structured data (`claude-memory.db`) |
| `duckduckgo` | Web search | Real-time internet search |
| `github` | GitHub API | Search repos, read files, manage PRs/Issues |

> **Note**: Built-in `WebSearch` tool only searches Cursor docs in this environment.
> Use `duckduckgo` MCP for general web search. GitHub PAT is pre-configured.

---

## 4. Custom Skills (`~/.claude/skills/`)

Invoke explicitly with `/skill-name` or auto-triggered by context.

| Skill | Trigger Scenario |
|-------|------------------|
| `/debug` | Errors, crashes, unexpected behavior |
| `/review` | Code review (security / performance / correctness) |
| `/test` | Generate tests (Jest / Vitest / pytest) |
| `/tdd` | Test-driven development (Red-Green-Refactor) |
| `/security-audit` | Security audit (OWASP Top 10) |
| `/api-design` | REST API design patterns |
| `/docker` | Dockerfile / Docker Compose patterns |
| `/db-migration` | Zero-downtime schema changes |
| `/deploy` | CI/CD pipelines, blue-green / canary deploy |
| `/mcp-builder` | Build new MCP servers |
| `/refactor` | Code refactoring (behavior-preserving) |
| `/perf` | Performance bottleneck diagnosis |
| `/explain` | Explain code / architecture / algorithms |
| `/git-workflow` | Conventional commits, branching, conflict resolution |
| `/scaffold` | New project scaffold (Next.js / Express / FastAPI / CLI / MCP) |
| `/context7-lookup` | Query latest official docs for any library |
| `/pr-review` | Pull request review |
| `/deep-research` | Multi-source research with citations |

---

## 5. Working Rules

1. **Language**: Always reply in Chinese; keep code in its original language
2. **File ops**: Always read a file before modifying it
3. **Git**: Commit messages in Chinese; never auto-push
4. **Concise**: Lead with answer/code, skip preamble
5. **Minimal changes**: Don't add unrequested features, refactors, or dependencies
6. **Security**: Never expose secrets; follow OWASP Top 10
7. **Docs**: For library docs, use `context7` MCP (add `use context7` to prompt)
8. **GitHub**: Use `github` MCP or `curl + GitHub API` directly (PAT pre-configured)

---

## 6. Memory Systems

| System | Path / Notes |
|--------|--------------|
| Auto memory | `C:/Users/KAKA/.claude/projects/.../memory/MEMORY.md` (auto-loaded each session) |
| MCP memory | Cross-session entity store (knowledge graph) |
| SQLite | `C:/Users/KAKA/claude-memory.db` (structured persistent data) |
| Knowledge files | `memory/mcp-servers.md`, `memory/claude-code-skills.md`, `memory/top-starred-projects.md` |

---

## 7. Skill File Format

All skills live in `~/.claude/skills/` as Markdown with YAML frontmatter:

```markdown
---
name: skill-name
description: When to trigger (agent uses this to determine relevance)
argument-hint: [optional param hint]
allowed-tools: Read, Write, Bash
---

# Skill content...
```

Sources: [everything-claude-code](https://github.com/affaan-m/everything-claude-code) · [awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code) · [antigravity-awesome-skills](https://github.com/sickn33/antigravity-awesome-skills)

---

## 8. Next Improvement Plan

### Round 2 Roadmap

| Priority | Item | Description |
|----------|------|-------------|
| P0 | Add skill files to repo | Upload all 18 `~/.claude/skills/*.md` files to `skills/` folder |
| P0 | Add `settings.json` template | Sanitized config template (no secrets) for quick replication |
| P1 | CLAUDE.md global template | Global instructions file at `~/.claude/CLAUDE.md` |
| P1 | Hooks setup guide | Pre/post tool hooks for automated behaviors |
| P1 | Add more domain skills | Frontend (React/Vue), data science, infra-as-code (Terraform) |
| P2 | MCP server configs | Example configs for each MCP with setup instructions |
| P2 | Troubleshooting guide | Common issues: proxy errors, MCP failures, encoding problems |
| P2 | Agent orchestration skill | Multi-agent coordination patterns |
| P3 | Auto-sync script | Script to sync local skills dir to this repo |

---

*Generated: 2026-03-21 | Model: claude-opus-4-6*
