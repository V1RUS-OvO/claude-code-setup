# claude-code-setup

> **Private configuration repository for KAKA's Claude Code environment.**
> New agents: read this file top-to-bottom before doing anything else.

---

## Repository Structure

```
claude-code-setup/
├── README.md                    # This file — agent onboarding
├── config/
│   ├── settings.template.json   # Claude Code settings (secrets removed)
│   └── CLAUDE.md                # Global agent instructions template
├── skills/                      # 18 custom skills for ~/.claude/skills/
│   ├── debug.md
│   ├── review.md
│   ├── security-audit.md
│   └── ... (18 total)
├── memory/
│   ├── MEMORY.md                # Auto-loaded memory (project context)
│   ├── mcp-servers.md           # Full MCP server reference
│   ├── claude-code-skills.md    # Skills & hooks reference
│   └── top-starred-projects.md  # High-value GitHub projects for AI coding
└── docs/
    ├── mcp-setup.md             # How to configure each MCP
    ├── skills-guide.md          # How skills work & how to write new ones
    └── troubleshooting.md       # Common issues & fixes
```

---

## Quick Start (New Machine Setup)

```bash
# 1. Clone this repo
git clone https://github.com/V1RUS-OvO/claude-code-setup.git

# 2. Copy skills
mkdir -p ~/.claude/skills
cp claude-code-setup/skills/*.md ~/.claude/skills/

# 3. Copy settings template and fill in your secrets
cp claude-code-setup/config/settings.template.json ~/.claude/settings.json
# Edit: add your ANTHROPIC_AUTH_TOKEN, GITHUB_PERSONAL_ACCESS_TOKEN

# 4. Copy global CLAUDE.md
cp claude-code-setup/config/CLAUDE.md ~/.claude/CLAUDE.md
```

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
| GitHub CLI | Not installed (use `github` MCP instead) |

---

## 2. Claude Code Config

| Item | Value |
|------|-------|
| Model | `claude-opus-4-6` |
| API Proxy | `https://uniquefox.top` |
| Config file | `C:/Users/KAKA/.claude/settings.json` |
| Extended Thinking | Enabled (`alwaysThinkingEnabled: true`) |
| Reply language | **Chinese (Simplified)** |
| Skills dir | `C:/Users/KAKA/.claude/skills/` |
| Memory file | `C:/Users/KAKA/.claude/projects/.../memory/MEMORY.md` |

See `config/settings.template.json` for the full config structure.

---

## 3. MCP Servers (9 active)

| Name | Package | Capability | Key Use |
|------|---------|-----------|----------|
| `playwright` | `@playwright/mcp` | Browser automation | Web interaction, screenshots, E2E tests |
| `context7` | `@upstash/context7-mcp` | Live library docs | Latest API docs for any library |
| `memory` | `@modelcontextprotocol/server-memory` | Persistent memory | Cross-session knowledge graph |
| `sequential-thinking` | `@modelcontextprotocol/server-sequential-thinking` | Reasoning chains | Multi-step problem decomposition |
| `filesystem` | `@modelcontextprotocol/server-filesystem` | File system | Read/write `C:/Users/KAKA` |
| `fetch` | `fetch-mcp` | HTTP fetch | Scrape pages / call APIs |
| `sqlite` | `mcp-server-sqlite` | Local DB | Persistent structured data |
| `duckduckgo` | `duckduckgo-mcp-server` | Web search | Real-time internet search |
| `github` | `@modelcontextprotocol/server-github` | GitHub API | Repos, files, PRs, Issues |

**Critical notes:**
- Built-in `WebSearch` tool only searches Cursor docs — use `duckduckgo` MCP for real web search
- `github` MCP has PAT configured — use it instead of `gh` CLI
- GitHub API also accessible via `curl` with the PAT in settings.json
- `context7`: add `use context7` to any prompt to query live library docs

See `docs/mcp-setup.md` for installation and configuration details.

---

## 4. Custom Skills (18 total)

Location: `~/.claude/skills/` | Source: `skills/` in this repo

Invoke with `/skill-name` or auto-triggered by context matching `description` field.

### Development Workflow
| Skill | File | Auto-trigger when... |
|-------|------|----------------------|
| `/debug` | `debug.md` | Error, crash, unexpected behavior |
| `/review` | `review.md` | Asked to review/audit code |
| `/test` | `test.md` | Asked to write tests |
| `/tdd` | `tdd.md` | Writing new feature (test-first) |
| `/refactor` | `refactor.md` | Asked to clean up / restructure |
| `/explain` | `explain.md` | Asked what code does |

### Architecture & Design
| Skill | File | Auto-trigger when... |
|-------|------|----------------------|
| `/api-design` | `api-design.md` | Designing REST endpoints |
| `/security-audit` | `security-audit.md` | Auth, uploads, payments, secrets |
| `/db-migration` | `db-migration.md` | Schema changes, ALTER TABLE |
| `/docker` | `docker.md` | Dockerfile, docker-compose |
| `/deploy` | `deploy.md` | CI/CD, production release |
| `/scaffold` | `scaffold.md` | New project / module creation |

### Productivity
| Skill | File | Auto-trigger when... |
|-------|------|----------------------|
| `/git-workflow` | `git-workflow.md` | Commits, branches, conflicts |
| `/pr-review` | `pr-review.md` | Reviewing a pull request |
| `/perf` | `perf.md` | Slow app, high resource usage |
| `/deep-research` | `deep-research.md` | Research, investigate, deep dive |
| `/context7-lookup` | `context7-lookup.md` | Library API questions |
| `/mcp-builder` | `mcp-builder.md` | Building MCP servers |

See `docs/skills-guide.md` for how to write new skills.

---

## 5. Working Rules

These rules apply to ALL agents working in this environment:

### Communication
- **Always reply in Chinese** (Simplified); keep code in original language
- Be concise — lead with answer/code, skip preamble
- Use `file:line` references when pointing to code locations

### Code Changes
- Always read a file before modifying it
- Make minimal changes — don't refactor unrequested code
- Don't add features, error handling, or abstractions beyond what's asked
- No docstrings/comments unless logic is non-obvious

### Git
- Commit messages in Chinese
- Never auto-push without explicit request
- Prefer new commits over amending

### Security
- Never expose or log secrets
- Follow OWASP Top 10 for all user-facing code
- Validate at system boundaries (user input, external APIs)

### Tool Priority
- Library docs → `context7` MCP (`use context7`)
- Web search → `duckduckgo` MCP
- GitHub operations → `github` MCP or `curl + GitHub API`
- File search → `Glob`/`Grep` tools (not `find`/`grep` in bash)

---

## 6. Memory Systems

| System | Location | Purpose |
|--------|----------|---------|
| Auto memory | `~/.claude/projects/.../memory/MEMORY.md` | Auto-loaded each session; key facts & preferences |
| Knowledge base | `memory/*.md` in this repo | Reference docs (MCP list, skills guide, top projects) |
| MCP memory | `@modelcontextprotocol/server-memory` | Cross-session entity graph |
| SQLite | `C:/Users/KAKA/claude-memory.db` | Structured persistent data |

When starting a new session, check `MEMORY.md` for context before proceeding.

---

## 7. Roadmap

- [x] 18 custom skills uploaded
- [x] Settings template
- [x] Global CLAUDE.md
- [x] Memory knowledge base files
- [ ] Domain skills: React/Vue, data science, Terraform
- [ ] Hooks configuration guide
- [ ] Agent orchestration skill
- [ ] Auto-sync script (local skills → this repo)

---

*Last updated: 2026-03-21 | Model: claude-opus-4-6 | Owner: V1RUS-OvO*
