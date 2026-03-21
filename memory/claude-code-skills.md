# Claude Code Skills、Hooks 和最佳实践

---

## 内置 Slash Commands

| 命令 | 功能 |
|------|------|
| `/help` | 帮助 |
| `/clear` | 清空上下文 |
| `/compact` | 压缩上下文 |
| `/model` | 切换模型 |
| `/fast` | 切换 Fast mode |
| `/memory` | 查看/编辑记忆文件 |
| `/mcp` | 管理 MCP 服务器 |
| `/hooks` | 管理 Hooks |
| `/permissions` | 管理权限 |
| `/tasks` | 查看任务列表 |
| `/commit` | 创建 git commit |
| `/review-pr` | 审查 PR |
| `/init` | 初始化项目 CLAUDE.md |
| `/rename` | 重命名会话 |
| `/cost` | 查看 token 用量 |
| `/status` | 状态栏配置 |
| `/terminal-setup` | 终端快捷键设置 |
| `/bug` | 报告 bug |
| `/login` / `/logout` | 账号管理 |

---

## 自定义 Skills

### 存放位置（优先级从高到低）
- `.claude/skills/` — 项目级
- `~/.claude/skills/` — 全局用户级

### Skill 文件格式（Markdown frontmatter）
```markdown
---
description: 描述什么时候触发（agent 用来判断相关性）
argument-hint: 参数提示（可选）
allowed-tools: Bash, Read, Write  # 限制可用工具（可选）
disable-model-invocation: true  # 设为 true 则只能显式调用（/skill-name）
---

# Skill 名称
具体指令内容...
```

### 推荐自定义 Skill 集

**`~/.claude/skills/commit.md`**
```markdown
---
description: Create a conventional commit with proper formatting
---
Analyze staged changes and create a conventional commit.
Format: type(scope): description
Types: feat, fix, docs, style, refactor, perf, test, chore
Keep subject under 72 chars. Add body if complex.
```

**`~/.claude/skills/review.md`**
```markdown
---
description: Review code for bugs, security issues, and best practices
---
Review thoroughly for:
- Security vulnerabilities (OWASP top 10)
- Performance issues and bottlenecks
- Code style and best practices
- Edge cases and error handling
Provide specific, actionable feedback with line references.
```

**`~/.claude/skills/test.md`**
```markdown
---
description: Generate comprehensive tests for the current file or function
---
Generate tests covering:
- Happy path scenarios
- Edge cases and boundary conditions
- Error handling and exceptions
- Mock external dependencies
Use the existing test framework in this project.
```

**`~/.claude/skills/explain.md`**
```markdown
---
description: Explain what this code does in simple terms
---
Explain:
1. High-level purpose
2. Step-by-step logic
3. Key design decisions
4. Potential improvements
```

**`~/.claude/skills/fix.md`**
```markdown
---
description: Diagnose and fix the bug or error shown
---
1. Identify root cause
2. Explain what went wrong
3. Apply minimal targeted fix
4. Verify fix doesn't break other things
```

**`~/.claude/skills/optimize.md`**
```markdown
---
description: Optimize code for performance
---
1. Identify bottlenecks
2. Apply targeted optimizations
3. Maintain readability
4. Document improvements
```

**`~/.claude/skills/docs.md`**
```markdown
---
description: Generate documentation for this code
---
Generate:
- Function/class purpose
- Parameters and return values
- Usage examples
- Edge cases and limitations
Match the existing doc style in this project.
```

**`~/.claude/skills/security.md`**
```markdown
---
description: Security audit the code for vulnerabilities
---
Audit for:
- Injection attacks (SQL, XSS, command injection)
- Authentication/authorization flaws
- Sensitive data exposure
- Insecure dependencies
- OWASP Top 10
Rate severity: Critical/High/Medium/Low
```

---

## Hooks 完整参考

### Hook 事件列表

| 事件 | 触发时机 | 可阻断? |
|------|---------|--------|
| `PreToolUse` | 工具调用前 | 是 |
| `PostToolUse` | 工具成功后 | 否 |
| `PostToolUseFailure` | 工具失败后 | 否 |
| `Stop` | Claude 停止时 | 可触发后续 |
| `SessionStart` | 会话开始 | 否 |
| `UserPromptSubmit` | 用户提交提示 | 是 |
| `PreCompact` | 压缩上下文前 | 否 |
| `PostCompact` | 压缩上下文后 | 否 |
| `Notification` | 通知时 | 否 |

### 常用 Hook 模式

**自动 Prettier 格式化**
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write|Edit",
      "hooks": [{
        "type": "command",
        "command": "jq -r '.tool_input.file_path // .tool_response.filePath' | { read -r f; prettier --write \"$f\"; } 2>/dev/null || true"
      }]
    }]
  }
}
```

**自动 ESLint 修复**
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write|Edit",
      "hooks": [{
        "type": "command",
        "command": "jq -r '.tool_input.file_path // empty' | grep -E '\\.(js|ts|jsx|tsx)$' | { read -r f; [ -n \"$f\" ] && npx eslint --fix \"$f\"; } 2>/dev/null || true"
      }]
    }]
  }
}
```

**自动运行测试**
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write|Edit",
      "hooks": [{
        "type": "command",
        "command": "jq -r '.tool_input.file_path' | grep -E '\\.(ts|js)$' && npm test -- --passWithNoTests 2>/dev/null || true"
      }]
    }]
  }
}
```

**记录所有 Bash 命令**
```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Bash",
      "hooks": [{
        "type": "command",
        "command": "jq -r '\"[\" + (now | todate) + \"] \" + .tool_input.command' >> ~/.claude/bash-history.log"
      }]
    }]
  }
}
```

**会话结束提醒提交**
```json
{
  "hooks": {
    "Stop": [{
      "hooks": [{
        "type": "command",
        "command": "echo '{\"systemMessage\": \"Session done! Check: git status\"}'"
      }]
    }]
  }
}
```

**危险命令拦截（PreToolUse 阻断）**
```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Bash",
      "hooks": [{
        "type": "command",
        "command": "cmd=$(jq -r '.tool_input.command'); echo \"$cmd\" | grep -qE '(rm -rf|drop table|DELETE FROM)' && echo '{\"continue\":false,\"stopReason\":\"Dangerous command blocked\"}' || true"
      }]
    }]
  }
}
```

### Hook 输入格式（stdin JSON）
```json
{
  "session_id": "abc123",
  "tool_name": "Write",
  "tool_input": { "file_path": "/path/to/file", "content": "..." },
  "tool_response": { "success": true }  // PostToolUse only
}
```

### Hook 输出格式（stdout JSON）
```json
{
  "continue": false,           // false = 阻断操作
  "stopReason": "原因说明",
  "systemMessage": "显示给用户的消息",
  "decision": "block",
  "reason": "详细说明"
}
```

---

## CLAUDE.md 最佳实践

### 项目级 `.claude/CLAUDE.md`
```markdown
# Project Name

## Tech Stack
- Language: TypeScript
- Framework: Next.js 15
- Database: PostgreSQL
- Package manager: pnpm

## Commands
- `pnpm dev` - 启动开发服务器
- `pnpm test` - 运行测试
- `pnpm build` - 构建
- `pnpm lint` - 代码检查

## Architecture
- src/app/ - Next.js App Router 页面
- src/components/ - React 组件
- src/lib/ - 工具函数
- src/server/ - 服务端逻辑

## Code Style
- 缩进：2 spaces
- 引号：single quotes
- 命名：camelCase 变量，PascalCase 组件
- 始终使用 async/await

## Important Rules
- 不要修改 src/generated/ 下的文件（自动生成）
- 提交前必须通过 lint
- API keys 存放在 .env.local
```

### 全局 `~/.claude/CLAUDE.md`
```markdown
# Global Instructions

## Communication
- 用中文回复
- 简洁直接，不废话
- 代码优先，减少解释

## Coding Preferences
- 优先 TypeScript
- async/await 优先于 Promise chains
- 最小化改动，不引入不必要的依赖
- 修改前先读文件

## Git
- commit message 用中文
- 不要自动 push，除非明确要求
```

---

## Permissions 推荐配置

```json
{
  "permissions": {
    "allow": [
      "Bash(git:*)",
      "Bash(npm:*)",
      "Bash(npx:*)",
      "Bash(pnpm:*)",
      "Bash(bun:*)",
      "Bash(node:*)",
      "Bash(python:*)",
      "Bash(pip:*)",
      "Bash(uv:*)",
      "Bash(ls:*)",
      "Bash(mkdir:*)",
      "Bash(cp:*)",
      "Bash(mv:*)",
      "Bash(echo:*)",
      "Bash(cat:*)"
    ]
  }
}
```

---

## 有用的第三方生态

- **aider** - AI pair programming CLI，支持 Claude
- **continue.dev** - VS Code/JetBrains AI 插件
- **open-interpreter** - 本地代码执行 AI
- **agentops** - AI agent 监控调试
- **e2b.dev** - 安全代码执行沙箱
