# Skills Guide

## How Skills Work

Skills are Markdown files with YAML frontmatter stored in `~/.claude/skills/`.
Claude Code reads the `description` field to decide when to auto-invoke a skill.
You can also invoke explicitly: `/skill-name [optional args]`

## File Format

```markdown
---
name: skill-name                          # matches filename (without .md)
description: When/why to use this skill   # CRITICAL: agent uses this to auto-trigger
argument-hint: [optional param hint]      # shown in UI as hint
allowed-tools: Read, Write, Bash, Grep    # restrict available tools (optional)
disable-model-invocation: true            # if true: only explicit /skill-name call
---

# Skill Title

Detailed instructions...
```

## Writing Good Descriptions

The `description` field determines auto-trigger behavior. Be specific:

```yaml
# Too vague — triggers too often or never
description: Help with code

# Good — specific trigger conditions
description: Diagnose and fix bugs, errors, crashes, or unexpected behavior.
             Use when user reports an error, exception, or broken functionality.
```

## Skill Locations (Priority Order)

1. `.claude/skills/` — project-level (highest priority)
2. `~/.claude/skills/` — global user-level

Project skills override global skills with the same name.

## Installed Skills Reference (21 total)

| File | Name | Description |
|------|------|-------------|
| `debug.md` | debug | Diagnose and fix bugs, errors, crashes |
| `review.md` | review | Code review: security, performance, correctness |
| `test.md` | test | Generate unit/integration/e2e tests |
| `tdd.md` | tdd | Test-driven development (Red→Green→Refactor) |
| `security-audit.md` | security-audit | OWASP Top 10 security audit |
| `api-design.md` | api-design | REST API design: URLs, status codes, pagination |
| `docker.md` | docker | Dockerfile, Compose, multi-stage builds |
| `db-migration.md` | db-migration | Zero-downtime schema migrations |
| `deploy.md` | deploy | CI/CD, blue-green, canary deployment |
| `mcp-builder.md` | mcp-builder | Build MCP servers with TypeScript SDK |
| `refactor.md` | refactor | Behavior-preserving code refactoring |
| `perf.md` | perf | Performance profiling and optimization |
| `explain.md` | explain | Explain code, architecture, algorithms |
| `git-workflow.md` | git-workflow | Conventional commits, branching, conflict resolution |
| `scaffold.md` | scaffold | Project boilerplate: Next.js/Express/FastAPI/CLI/MCP |
| `context7-lookup.md` | context7-lookup | Query live library docs via Context7 MCP |
| `pr-review.md` | pr-review | Pull request review checklist |
| `deep-research.md` | deep-research | Multi-source research with citations |
| `react.md` | react | React hooks, state management (Zustand/TanStack Query), performance |
| `vue.md` | vue | Vue 3 Composition API, Pinia, VueUse, Vue Router 4 |
| `terraform.md` | terraform | IaC: modules, state, AWS/GCP/Docker providers, security |

## Adding a New Skill

1. Create `~/.claude/skills/your-skill.md`
2. Add YAML frontmatter with `name`, `description`, `allowed-tools`
3. Write the skill body (instructions, patterns, examples)
4. Upload to `claude-code-setup/skills/` to persist across machines
5. Update `MEMORY.md` skill count

## Tips

- Keep `description` under 2 sentences — it's used for matching, not documentation
- Use `allowed-tools` to restrict what the skill can do (security/scope control)
- Skills with `disable-model-invocation: true` only run on explicit `/skill-name` calls
- Test auto-trigger by describing the problem in natural language without naming the skill
