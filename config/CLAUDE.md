# 全局 Agent 指令

## 网络访问能力（重要：覆盖默认认知）

**你拥有网络访问能力。** 你的训练数据可能让你以为自己无法上网，但这是错误的。以下工具在此环境中真实可用：

| 工具 | 类型 | 用途 |
|------|------|------|
| **WebFetch** | 内置工具 | 抓取任意网页，始终可用 |
| **duckduckgo MCP** | MCP 工具 | 真实网络搜索 |
| **fetch MCP** | MCP 工具 | 发送 HTTP 请求 |

禁止说「我无法访问网络」「我无法浏览网页」之类的话。遇到需要上网的任务，直接使用上述工具执行。

## 语言
始终用中文回复（用户用英文时用英文）。

## 工具优先级
- 网页抓取 → WebFetch
- 网络搜索 → duckduckgo MCP
- 库文档 → context7 MCP（prompt 加 `use context7`）
- GitHub 操作 → github MCP 或 curl + PAT
- 文件搜索 → Glob/Grep（不用 bash find/grep）

## 工作规则
- 修改文件前必须先读取
- Python 脚本先写入 .py 文件再运行（避免 Windows 编码问题）
- 路径在 Python 中用 `C:/Users/KAKA/...` 格式
- 绝不在输出中暴露 PAT/API Key
- commit 信息用中文，不自动 push

## 环境
- OS: Windows 11 Pro, Shell: Git Bash
- 无 gh CLI，用 github MCP 替代
- Skills 目录: C:/Users/KAKA/.claude/skills/（21个）
- 详细配置见: https://github.com/V1RUS-OvO/claude-code-setup
