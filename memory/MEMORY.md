# Memory

## 用户配置
- API 代理: https://uniquefox.top
- 模型: claude-opus-4-6
- 配置文件: C:/Users/KAKA/.claude/settings.json
- `alwaysThinkingEnabled: true` 已启用（效果取决于代理是否支持）
- 语言偏好: 中文回复

## 知识库文件
- [MCP Servers 完整参考](./mcp-servers.md) - 官方和第三方 MCP 服务器列表、安装方法
- [Claude Code Skills 和最佳实践](./claude-code-skills.md) - Skills、Hooks、CLAUDE.md 模板

## 知识库文件（续）
- [50k+ Star 顶级项目参考](./top-starred-projects.md) - 对 AI 编码最有帮助的高 star 项目汇总

## 网络环境说明
- 当前环境无法访问 github.com、modelcontextprotocol.io、mcp.so 等外部域名
- WebSearch 工具在此环境只能搜索 Cursor 文档，不支持通用网络搜索
- 真实网络搜索 → 用 **duckduckgo MCP**
- GitHub 操作 → 用 **github MCP**（已配置 PAT，无需 gh CLI）
- 库文档查询 → 用 **context7 MCP**（在 prompt 加 `use context7`）

## 环境信息（来自 claude-code-setup）
- OS: Windows 11 Pro, Shell: Git Bash (MINGW64)
- Node.js: v24.14.0, npm: 11.9.0, Python: 3.11.9, Docker: 28.3.3
- 无 gh CLI，用 github MCP 替代
- 配置仓库: https://github.com/V1RUS-OvO/claude-code-setup（私有）

## 活跃 MCP Servers（9个）
playwright、context7、memory、sequential-thinking、filesystem、fetch、sqlite、duckduckgo、github

## Claude Code Skills（~/.claude/skills/）
已安装 18 个自定义 skills（来源：claude-code-setup/skills/）：
- `debug` — 诊断和修复 bug
- `review` — 代码审查（安全/性能/正确性）
- `test` — 生成测试用例
- `tdd` — 测试驱动开发流程
- `security-audit` — 安全审计（OWASP）
- `api-design` — REST API 设计规范
- `docker` — Docker/Compose 模式
- `db-migration` — 数据库迁移最佳实践
- `deploy` — CI/CD 和部署模式
- `mcp-builder` — 构建 MCP 服务器
- `refactor` — 代码重构
- `perf` — 性能优化
- `explain` — 代码解释
- `git-workflow` — Git 工作流和 Conventional Commits
- `scaffold` — 项目脚手架
- `context7-lookup` — 查询最新库文档
- `pr-review` — PR 审查
- `deep-research` — 多源深度研究
- `react` — React hooks、状态管理、性能优化
- `vue` — Vue 3 Composition API、Pinia、VueUse
- `terraform` — IaC 模块化、状态管理、AWS/Docker provider

## 推荐安装的 MCP Servers
1. `npx @playwright/mcp@latest` - 浏览器自动化
2. `npx -y @modelcontextprotocol/server-filesystem <path>` - 文件访问
3. `npx -y @modelcontextprotocol/server-github` - GitHub 操作
4. `npx -y @modelcontextprotocol/server-memory` - 持久记忆
5. `npx -y @upstash/context7-mcp` - 实时文档查询
6. `npx -y @modelcontextprotocol/server-sequential-thinking` - 复杂推理
