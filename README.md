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
├── skills/                      # 21 custom skills for ~/.claude/skills/
│   ├── debug.md
│   ├── react.md
│   ├── vue.md
│   ├── terraform.md
│   └── ... (21 total)
├── memory/
│   ├── MEMORY.md                # Auto-loaded memory (project context)
│   ├── mcp-servers.md           # Full MCP server reference
│   ├── claude-code-skills.md    # Skills & hooks reference
│   └── top-starred-projects.md  # High-value GitHub projects for AI coding
└── docs/
    ├── mcp-setup.md             # How to configure each MCP
    ├── skills-guide.md          # How skills work & how to write new ones
    ├── hooks-guide.md           # Hooks configuration & examples
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
# Edit: add your ANTHROPIC_AUTH_TOKEN, GITHUB_PERSONAL_ACCESS_TOKEN, WAKATIME_API_KEY

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
| GitHub CLI | Not installed (use `github` MCP instead) |

---

## 2. Claude Code Config

| Item | Value |
|------|-------|
| Model | `claude-opus-4-6` |
| API Proxy | `https://uniquefox.top` |
| Config file | `C:/Users/KAKA/.claude/settings.json` |
| Skills dir | `C:/Users/KAKA/.claude/skills/` (21 skills) |
| Memory dir | `C:/Users/KAKA/.claude/projects/C--Users-KAKA-Desktop/memory/` |
| Always Thinking | Enabled |

---

## 3. Active MCP Servers (9)

| Name | Package | Purpose |
|------|---------|----------|
| `playwright` | `@playwright/mcp` | Browser automation |
| `context7` | `@upstash/context7-mcp` | Live library docs |
| `memory` | `@modelcontextprotocol/server-memory` | Persistent memory |
| `sequential-thinking` | `@modelcontextprotocol/server-sequential-thinking` | Complex reasoning |
| `filesystem` | `@modelcontextprotocol/server-filesystem` | File access |
| `fetch` | `@modelcontextprotocol/server-fetch` | HTTP requests |
| `sqlite` | `@modelcontextprotocol/server-sqlite` | SQLite database |
| `duckduckgo` | `duckduckgo-mcp` | Web search |
| `github` | `@modelcontextprotocol/server-github` | GitHub API (PAT pre-configured) |

> No `gh` CLI installed. Use the `github` MCP for all GitHub operations.
> For web search, use `duckduckgo` MCP — not the built-in WebSearch tool.

---

## 3.5. Network Access Notes

- Agents in this environment **do have network access**
- For webpage fetching, prefer **WebFetch** (built-in)
- For web search, prefer **duckduckgo MCP**
- For HTTP requests / API calls, use **fetch MCP**
- Do **not** say "I cannot access the network" or "I cannot browse webpages" in this environment

---

## 4. Skills Reference (21 total)

| File | Trigger / Use Case |
|------|--------------------|
| `debug.md` | Diagnosing bugs, errors, crashes |
| `review.md` | Code review (security/perf/correctness) |
| `test.md` | Generating test cases |
| `tdd.md` | Test-driven development (Red→Green→Refactor) |
| `security-audit.md` | OWASP Top 10 security audit |
| `api-design.md` | REST API design patterns |
| `docker.md` | Dockerfile & Compose patterns |
| `db-migration.md` | Zero-downtime database migrations |
| `deploy.md` | CI/CD, blue-green, canary deployment |
| `mcp-builder.md` | Building MCP servers (TypeScript SDK) |
| `refactor.md` | Behavior-preserving code refactoring |
| `perf.md` | Performance profiling & optimization |
| `explain.md` | Code/architecture/algorithm explanation |
| `git-workflow.md` | Conventional commits, branching, conflicts |
| `scaffold.md` | Project boilerplate (Next.js/Express/FastAPI/MCP) |
| `context7-lookup.md` | Query live library docs via Context7 MCP |
| `pr-review.md` | Pull request review checklist |
| `deep-research.md` | Multi-source research with citations |
| `react.md` | React hooks, state management, performance |
| `vue.md` | Vue 3 Composition API, Pinia, VueUse |
| `terraform.md` | IaC modules, state management, AWS/Docker providers |

---

## 5. Docs Index

| File | Contents |
|------|----------|
| `docs/mcp-setup.md` | Installing & configuring each MCP server |
| `docs/skills-guide.md` | How skills work, frontmatter reference, writing new skills |
| `docs/hooks-guide.md` | Hook events, config format, 6 practical examples |
| `docs/troubleshooting.md` | MCP/API/encoding/path/Docker common issues |

---

## 6. GitHub Profile

- Profile: https://github.com/V1RUS-OvO
- Profile repo: `V1RUS-OvO/V1RUS-OvO` (auto-rendered as homepage)
- Features: typing animation, trophy wall, stats cards, activity graph, contribution snake, Wakatime stats
- Wakatime: configured, updates daily via GitHub Actions
- Snake animation: updates daily via GitHub Actions (output branch)

---

## 7. Working Rules

1. **Language**: Always reply in Chinese unless user writes in English
2. **GitHub ops**: Use `curl` + PAT from settings.json or the `github` MCP — no `gh` CLI
3. **Network access**: Agents in this environment can use `WebFetch`, `duckduckgo` MCP, and `fetch` MCP; do not incorrectly claim network access is unavailable
4. **Python scripts**: Write to `.py` file first, then run — avoids Windows encoding issues
5. **Paths**: Use `C:/Users/KAKA/...` style (not `/c/Users/...`) in Python
6. **Secrets**: Never log or expose PAT/API keys in output
7. **Memory**: Update `MEMORY.md` when skills/config changes
8. **Wakatime API Key**: Stored as GitHub secret `WAKATIME_API_KEY` in `V1RUS-OvO/V1RUS-OvO`
