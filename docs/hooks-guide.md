# Claude Code Hooks Configuration Guide

Hooks let you run shell commands automatically in response to Claude Code events — before/after tool calls, on session start/stop, etc. This enables automated workflows without manual intervention.

## How Hooks Work

```
User prompt -> Claude decides to use tool
                    |
              PreToolUse hook  <-- can BLOCK the tool call
                    |
              Tool executes
                    |
              PostToolUse hook <-- runs after success
                    |
              Claude responds
                    |
              Stop hook        <-- runs when Claude finishes
```

Hooks receive context via **stdin as JSON** and can optionally output JSON to influence behavior.

---

## Configuration Format

Hooks are configured in `~/.claude/settings.json` (global) or `<project>/.claude/settings.json` (project-level).

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "your-script-or-inline-command"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "your-script-or-inline-command"
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "your-script-or-inline-command"
          }
        ]
      }
    ]
  }
}
```

### Structure Rules
- Top-level key = event name
- Each event takes an array of `{ matcher, hooks[] }` objects
- `matcher` = regex matched against `tool_name` (omit for non-tool events)
- Each hook must have `type: "command"` and a `command` string

---

## Supported Events

| Event | Trigger | Has matcher | Can block? |
|-------|---------|-------------|------------|
| `PreToolUse` | Before tool executes | Yes | Yes |
| `PostToolUse` | After tool succeeds | Yes | No |
| `PostToolUseFailure` | After tool fails | Yes | No |
| `Stop` | When Claude finishes responding | No | No |
| `SessionStart` | When session begins | No | No |
| `UserPromptSubmit` | When user submits a prompt | No | Yes |
| `PreCompact` | Before context compaction | No | No |
| `PostCompact` | After context compaction | No | No |
| `Notification` | When a notification fires | No | No |

---

## stdin JSON Schema

Claude Code pipes JSON into your hook command via stdin:

```json
{
  "session_id": "abc123",
  "tool_name": "Write",
  "tool_input": {
    "file_path": "/path/to/file.ts",
    "content": "..."
  },
  "tool_response": {
    "success": true
  }
}
```

`tool_response` is only present in `PostToolUse` hooks.

### Common tool_input fields

| Tool | Key fields in tool_input |
|------|-------------------------|
| `Bash` | `command` |
| `Write` | `file_path`, `content` |
| `Edit` | `file_path`, `old_string`, `new_string` |
| `Read` | `file_path` |
| `Glob` | `pattern`, `path` |
| `Grep` | `pattern`, `path` |

---

## Matcher Patterns

`matcher` is a regex matched against `tool_name`:

```json
"matcher": "Bash"              // exact tool
"matcher": "Write|Edit"        // multiple tools
"matcher": "Write|Edit|MultiEdit"  // all write operations
"matcher": ".*"                // all tools
```

---

## Hook Output

Hooks can print JSON to **stdout** to influence Claude:

### Block a tool call (PreToolUse only)
```json
{ "continue": false, "stopReason": "Reason shown to user" }
```

### Inject a system message
```json
{ "systemMessage": "Reminder: always run tests after editing" }
```

### Do nothing (default)
Exit with code 0 and no output — tool proceeds normally.

---

## Practical Examples

### 1. Auto-format on file save
Run Prettier after every Write/Edit:
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write|Edit",
      "hooks": [{
        "type": "command",
        "command": "f=$(jq -r .tool_input.file_path); ext=${f##*.}; case $ext in ts|tsx|js|jsx|json|css|md) npx prettier --write '$f' 2>/dev/null || true ;; esac"
      }]
    }]
  }
}
```

### 2. Block dangerous Bash commands
Prevent `rm -rf`, `DROP TABLE`, etc.:
```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Bash",
      "hooks": [{
        "type": "command",
        "command": "cmd=$(jq -r .tool_input.command); echo "$cmd" | grep -qE "(rm -rf /|DROP TABLE|DELETE FROM .* WHERE 1)" && echo '{"continue":false,"stopReason":"Dangerous command blocked by hook"}' || true"
      }]
    }]
  }
}
```

### 3. Log all Bash commands
Keep an audit trail:
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Bash",
      "hooks": [{
        "type": "command",
        "command": "jq -r '[now|todate] + " " + .tool_input.command' >> ~/.claude/bash-history.log"
      }]
    }]
  }
}
```

### 4. Git status reminder on session end
```json
{
  "hooks": {
    "Stop": [{
      "hooks": [{
        "type": "command",
        "command": "echo '{"systemMessage": "Session ended. Run git status to review changes."}'"
      }]
    }]
  }
}
```

### 5. Run tests after editing source files
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write|Edit",
      "hooks": [{
        "type": "command",
        "command": "f=$(jq -r .tool_input.file_path); echo "$f" | grep -qE "\\.(ts|tsx|py)$" && echo '{"systemMessage": "Source file changed. Consider running tests."}' || true"
      }]
    }]
  }
}
```

### 6. Windows-specific: notify on task complete
Using PowerShell toast notification:
```json
{
  "hooks": {
    "Stop": [{
      "hooks": [{
        "type": "command",
        "command": "powershell -Command "[System.Reflection.Assembly]::LoadWithPartialName('System.Windows.Forms'); [System.Windows.Forms.MessageBox]::Show('Claude finished!', 'Claude Code')""
      }]
    }]
  }
}
```

---

## Managing Hooks

### Via /update-config skill (recommended)
In Claude Code, use the `update-config` skill to safely add hooks without manually editing JSON:
```
/update-config
```
Describe the hook behavior you want and Claude will configure it correctly.

### Via /hooks command
In the Claude Code REPL:
```
/hooks
```
Opens the interactive hooks manager — browse, add, toggle, and delete hooks.

### Manual edit
Direct edit of `~/.claude/settings.json`. Be careful with JSON syntax — a parse error disables all hooks silently.

---

## Windows Notes

This setup runs on **Windows 11 with Git Bash**. Key considerations:

| Issue | Solution |
|-------|----------|
| Path separators | Use forward slashes in hook commands: `C:/Users/...` |
| `jq` not available | Install via `winget install jqlang.jq` or use Python: `python3 -c "import json,sys; d=json.load(sys.stdin); print(d['tool_input']['file_path''])"` |
| Shell is bash | Commands run in Git Bash, not CMD/PowerShell |
| Encoding | Use `utf-8` explicitly in Python scripts |
| PowerShell hooks | Prefix with `powershell -Command "..."` |

### jq-free alternative for parsing stdin
```bash
python3 -c "
import json, sys
d = json.load(sys.stdin)
cmd = d['tool_input'].get('command', '?')
print(f'[HOOK] Bash command: {cmd[:100]}')
" >> C:/Users/KAKA/.claude/hook.log
```

---

## Current Setup (V1RUS-OvO)

Configured in `C:/Users/KAKA/.claude/settings.json`. No hooks are currently active — this is a clean slate for adding automation.

**Recommended first hooks for AI coding workflow:**

1. **Stop hook** — remind to commit after session ends
2. **PostToolUse Write/Edit** — auto-format changed files
3. **PostToolUse Bash** — log commands for audit trail

To add any of these, run `/update-config` in Claude Code and describe what you want.

---

*Part of [claude-code-setup](https://github.com/V1RUS-OvO/claude-code-setup)*
