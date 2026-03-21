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

## Installed Skills Reference

| File | Name | Description Summary |
|------|------|---------------------|
| `debug.md` | debug | Diagnose errors and crashes |
| `review.md` | review | Code review: security, perf, correctness |
| `test.md` | test | Generate comprehensive test suites |
| `tdd.md` | tdd | Test-driven development (Red-Green-Refactor) |
| `security-audit.md` | security-audit | OWASP Top 10 security checklist |
| `api-design.md` | api-design | REST API design patterns |
| `docker.md` | docker | Dockerfile and Docker Compose patterns |
| `db-migration.md` | db-migration | Zero-downtime database schema changes |
| `deploy.md` | deploy | CI/CD pipelines and deployment strategies |
| `mcp-builder.md` | mcp-builder | Build MCP servers with TypeScript SDK |
| `refactor.md` | refactor | Behavior-preserving code refactoring |
| `perf.md` | perf | Performance bottleneck diagnosis & optimization |
| `explain.md` | explain | Explain code, architecture, algorithms |
| `git-workflow.md` | git-workflow | Conventional commits, branching, conflicts |
| `scaffold.md` | scaffold | New project boilerplate (Next.js/Express/FastAPI/MCP) |
| `context7-lookup.md` | context7-lookup | Query live library docs via Context7 |
| `pr-review.md` | pr-review | Pull request review checklist |
| `deep-research.md` | deep-research | Multi-source research with citations |

## Creating a New Skill

1. Create `~/.claude/skills/my-skill.md`
2. Add frontmatter with a precise `description`
3. Write clear, actionable instructions
4. Test by describing the trigger scenario in a prompt
5. Add to this repo: copy to `skills/my-skill.md` and commit

## Tips

- Keep each skill focused on one domain
- Reference available MCP tools in the skill if relevant
- Use concrete examples and checklists over prose
- Skills sourced from: [everything-claude-code](https://github.com/affaan-m/everything-claude-code), [awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code)
