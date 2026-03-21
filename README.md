# Agent 环境简报 · KAKA 工作站

> 用于向新 agent 模型快速交接环境配置、可用能力和工作规范。

---

## 一、基础环境

| 项目 | 值 |
|------|-----|
| 操作系统 | Windows 11 Pro (22631) |
| Shell | Git Bash (MINGW64) |
| Node.js | v24.14.0 |
| npm | 11.9.0 |
| Python | 3.11.9 |
| Git | 2.50.1 |
| Docker | 28.3.3 |
| VS Code | 已安装 |
| GitHub CLI | 未安装 |

---

## 二、Claude Code 配置

| 项目 | 值 |
|------|-----|
| 模型 |  |
| API 代理 |  |
| 配置文件 |  |
| Extended Thinking | 已启用 () |
| 回复语言 | **中文** |

---

## 三、已启用 MCP Servers

| MCP 名称 | 能力 | 关键用途 |
|----------|------|----------|
|  | 浏览器自动化 | 网页操作、截图、自动化测试 |
|  | 实时库文档查询 | 任意库的最新 API 文档 |
|  | 跨会话持久记忆 | 存储结构化知识实体 |
|  | 复杂推理链 | 多步骤问题分解 |
|  | 文件系统访问 | 读写  下任意文件 |
|  | HTTP 抓取 | 获取网页/API 内容 |
|  | 本地数据库 | 持久化结构化数据（） |
|  | 网页搜索 | 实时网络搜索 |
|  | GitHub API | 搜索仓库、读取文件、管理 PR/Issue |

> **注意**： 内置工具在此环境仅限 Cursor 文档，网络搜索请优先使用  MCP。

---

## 四、自定义 Skills（）

可通过  显式调用，或由 agent 根据上下文自动触发。

| Skill | 触发场景 |
|-------|----------|
|  | 报错、崩溃、异常行为排查 |
|  | 代码审查（安全/性能/正确性） |
|  | 生成测试用例（Jest/Vitest/pytest） |
|  | 测试驱动开发（Red→Green→Refactor） |
|  | 安全审计（OWASP Top 10） |
|  | REST API 设计规范 |
|  | Dockerfile / Docker Compose 模式 |
|  | 数据库 schema 变更（零停机） |
|  | CI/CD 流水线、蓝绿/金丝雀部署 |
|  | 构建新 MCP 服务器 |
|  | 代码重构（不改行为） |
|  | 性能瓶颈诊断与优化 |
|  | 解释代码/架构/算法 |
|  | Git 提交规范、分支策略、冲突解决 |
|  | 新项目脚手架（Next.js/Express/FastAPI/CLI/MCP） |
|  | 查询任意库的最新官方文档 |
|  | Pull Request 审查 |
|  | 多源深度研究，输出带引用报告 |

---

## 五、工作规范

1. **语言**：始终用中文回复，代码保持原语言
2. **文件操作**：修改前必须先读文件
3. **Git**：commit message 用中文，不自动 push
4. **简洁优先**：直接给答案/代码，减少废话
5. **最小改动**：不引入未被要求的功能、重构、依赖
6. **安全**：不暴露 secrets，注意 OWASP Top 10
7. **文档查询**：需要库文档时，优先用  MCP（加  指令）
8. **GitHub 访问**：直接用  MCP 或 

---

## 六、记忆系统

| 系统 | 路径/说明 |
|------|----------|
| 自动记忆 | （每次对话自动加载） |
| MCP memory | 跨会话实体存储（结构化知识图谱） |
| SQLite | （持久化结构化数据） |
| 知识库 | 、、 |

---

## 七、Skills 安装方法

所有 skill 文件存放于 ，格式为带 frontmatter 的 Markdown：



参考来源：[everything-claude-code](https://github.com/affaan-m/everything-claude-code) · [awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code) · [antigravity-awesome-skills](https://github.com/sickn33/antigravity-awesome-skills)

---

*生成时间: 2026-03-21 | 模型: claude-opus-4-6*
