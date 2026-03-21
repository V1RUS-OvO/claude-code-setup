# Troubleshooting Guide

## MCP Issues

### MCP server not starting
```
Symptom: Tool calls from MCP fail silently or "MCP not available"
Fix:
1. Check npx path: run `where npx` in terminal
2. Update settings.json command path to match
3. Restart Claude Code after settings change
4. Run the MCP manually to see error:
   npx -y @modelcontextprotocol/server-github
```

### GitHub MCP auth failure
```
Symptom: GitHub API returns 401
Fix: Verify PAT in settings.json has correct scopes:
  - repo (full)
  - read:org
  - read:user
Generate new PAT at: github.com/settings/tokens
```

### context7 not finding library
```
Symptom: "Library not found" or wrong docs returned
Fix:
1. Use official package name: "nextjs" not "next"
2. Be specific: "prisma" not "prisma orm"
3. Try resolve-library-id tool first to confirm ID
```

---

## API / Proxy Issues

### Connection refused / timeout
```
Symptom: Claude Code hangs or errors on startup
Check:
1. Proxy URL in settings.json env.ANTHROPIC_BASE_URL
2. Current proxy: https://uniquefox.top
3. Test: curl https://uniquefox.top/v1/models -H "Authorization: Bearer YOUR_KEY"
```

### Model not found
```
Symptom: "Model claude-opus-4-6 not found"
Fix: Verify proxy supports this model ID
Fallback: change model to "claude-opus-4-5" or "claude-sonnet-4-5"
```

---

## Encoding Issues (Windows)

### Chinese characters garbled in GitHub uploads
```
Symptom: README shows ??????? instead of Chinese
Root cause: Python on Windows defaults to GBK encoding in shell
Fix: Write content to file first, then read as bytes:
  with open('file.md', 'rb') as f:
      content = f.read()
  # Then base64 encode and upload
Alternative: Use English content for cross-platform compatibility
```

### Python heredoc fails in bash
```
Symptom: SyntaxError: (unicode error) unicodeescape
Fix: Write Python script to a .py file first, then run:
  python3 script.py
Don't pass multi-line Python with Chinese via << 'EOF' in bash
```

---

## Skills Not Triggering

```
Symptom: Skill exists but never auto-invokes
Check:
1. description field is specific enough
2. File is in ~/.claude/skills/ (not a subdirectory)
3. File has valid YAML frontmatter (no tabs, proper ---)
4. Try explicit invocation: /skill-name to verify it loads
```

---

## Git Bash Path Issues

```
Symptom: FileNotFoundError with /c/Users/... path in Python
Fix: Use Windows-style paths in Python:
  # Wrong:  '/c/Users/KAKA/.claude'
  # Right:  'C:/Users/KAKA/.claude'
  # Right:  r'C:\Users\KAKA\.claude'
```

---

## Docker Issues

```
Symptom: docker: command not found in Claude Code bash
Fix: Docker Desktop must be running
Check: docker info
Path: C:\Program Files\Docker\Docker\resources\bin\docker.exe
```
