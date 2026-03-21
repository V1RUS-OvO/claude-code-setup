# Global Agent Instructions

> This file lives at `~/.claude/CLAUDE.md` and applies to ALL Claude Code sessions.

---

## Communication
- Reply in **Chinese (Simplified)** always; code stays in original language
- Be concise — lead with answer or code, no preamble
- Use `file:line` format when referencing code locations
- No emojis unless explicitly requested

## Code Behavior
- Read files before modifying them
- Minimal changes only — don't refactor unrequested code
- Don't add features, helpers, error handling beyond what's asked
- No comments/docstrings unless logic is non-obvious
- No backwards-compat shims for removed code

## Git
- Commit messages in Chinese
- Never auto-push without explicit instruction
- Prefer new commits over `--amend`
- Never skip hooks (`--no-verify`)

## Security
- Never hardcode or expose secrets
- Validate all user inputs at system boundaries
- Follow OWASP Top 10 for web code

## Tool Usage Priority
- Library docs: use `context7` MCP (add `use context7` to prompt)
- Web search: use `duckduckgo` MCP
- GitHub: use `github` MCP or `curl + GitHub API`
- File search: use `Glob`/`Grep` tools, not bash `find`/`grep`
- File read: use `Read` tool, not `cat`

## Memory
- Check `MEMORY.md` at session start for project context
- Update memory files when learning stable new facts
- Don't save session-specific or temporary context to memory

## Available Skills
All skills in `~/.claude/skills/` are available. Key ones:
- `/debug` — diagnose errors and crashes
- `/review` — code review (security, perf, correctness)
- `/security-audit` — OWASP security checklist
- `/tdd` — test-driven development workflow
- `/deep-research` — multi-source research with citations
- `/scaffold` — new project boilerplate
- `/deploy` — CI/CD and deployment patterns
- `/mcp-builder` — build new MCP servers
