# MCP Servers 完整参考

> 更新于 2026年3月，来源：modelcontextprotocol/servers、punkpeye/awesome-mcp-servers
> 手动访问: https://github.com/punkpeye/awesome-mcp-servers

---

## 文件系统 & 文件管理

| 名称 | GitHub | 描述 |
|------|--------|------|
| **filesystem** | modelcontextprotocol/servers/src/filesystem | 官方本地文件读写，可配置访问控制 |
| **mcp-obsidian** | calclavia/mcp-obsidian | 读取和搜索 Obsidian vault |
| **mcp-filesystem-server** | mark3labs/mcp-filesystem-server | Go 实现的文件系统服务器 |
| **mcp-everything-search** | mamertofabian/mcp-everything-search | Windows Everything 搜索引擎集成 |

安装：`npx -y @modelcontextprotocol/server-filesystem <path>`

---

## 浏览器 & Web 自动化

| 名称 | GitHub | 描述 |
|------|--------|------|
| **playwright-mcp** | microsoft/playwright-mcp | 微软官方，浏览器自动化测试，**最推荐** |
| **puppeteer** | modelcontextprotocol/servers/src/puppeteer | 官方 Puppeteer 自动化：截图、点击、表单 |
| **mcp-fetch** | modelcontextprotocol/servers/src/fetch | 官方网页抓取转 Markdown |
| **browserbase-mcp-server** | browserbase/mcp-server-browserbase | 云端浏览器自动化 |
| **mcp-browser-use** | co-browser/browser-use-mcp-server | AI 驱动浏览器控制 |
| **mcp-selenium** | angiejones/mcp-selenium | Selenium WebDriver 集成 |

安装：`npx @playwright/mcp@latest`

---

## 数据库

| 名称 | GitHub | 描述 |
|------|--------|------|
| **sqlite** | modelcontextprotocol/servers/src/sqlite | 官方 SQLite 查询和 schema 检查 |
| **postgres** | modelcontextprotocol/servers/src/postgres | 官方 PostgreSQL 只读查询 |
| **mysql-mcp-server** | benborla/mcp-server-mysql | MySQL 集成 |
| **mcp-server-mongodb** | kiliczsh/mcp-mongo-server | MongoDB 操作 |
| **mcp-server-redis** | punkpeye/mcp-server-redis | Redis 键值存储 |
| **mcp-qdrant** | qdrant/mcp-server-qdrant | Qdrant 向量数据库语义搜索 |
| **supabase-mcp** | supabase-community/supabase-mcp | Supabase 数据库+Auth+Storage |
| **mcp-server-neon** | neondatabase/mcp-server-neon | Neon serverless Postgres |
| **mcp-server-bigquery** | LucasHild/mcp-server-bigquery | Google BigQuery 查询 |
| **mcp-server-duckdb** | ktanaka101/mcp-server-duckdb | DuckDB 分析数据库 |
| **mcp-server-elasticsearch** | elastic/mcp-server-elasticsearch | Elasticsearch 搜索和索引 |
| **airtable-mcp** | domdomegg/airtable-mcp-server | Airtable 读写 |
| **mcp-planetscale** | planetscale/mcp-server-planetscale | PlanetScale 云数据库 |
| **mcp-server-turso** | tursodatabase/mcp-server-turso | Turso edge 数据库 |
| **mcp-snowflake-server** | datawiz168/mcp-snowflake-service | Snowflake 数据仓库 |

---

## 版本控制 & 代码

| 名称 | GitHub | 描述 |
|------|--------|------|
| **github** | modelcontextprotocol/servers/src/github | 官方 GitHub：repo、PR、issue、分支、搜索 |
| **gitlab** | modelcontextprotocol/servers/src/gitlab | 官方 GitLab：项目、MR、issue |
| **git** | modelcontextprotocol/servers/src/git | 官方 Git 本地仓库操作 |
| **mcp-github-trending** | punkpeye/mcp-github-trending | 获取 GitHub 趋势仓库 |

安装：`npx -y @modelcontextprotocol/server-github`

---

## 开发工具 & 基础设施

| 名称 | GitHub | 描述 |
|------|--------|------|
| **mcp-server-code-runner** | formulahendry/mcp-server-code-runner | 多语言代码执行沙箱 |
| **e2b-mcp** | e2b-dev/mcp-server | E2B 安全沙箱执行 Python/JS |
| **mcp-server-docker** | ckreiling/mcp-server-docker | Docker 容器和镜像管理 |
| **mcp-server-kubernetes** | Flux159/mcp-server-kubernetes | K8s 集群管理 |
| **mcp-vercel** | Querio-ai/vercel-mcp | Vercel 部署管理 |
| **mcp-server-aws** | RafalWilinski/mcp-server-aws | AWS 服务集成 (S3/EC2/Lambda) |
| **mcp-server-terraform** | nwiizo/tfmcp | Terraform 基础设施管理 |
| **nx-mcp** | nrwl/nx-mcp | Nx monorepo 构建系统 |
| **mcp-server-npm** | TBXark/mcp-server-npmjs | npm 包搜索和元数据 |
| **mcp-package-version** | SimonB97/mcp-package-version | 跨生态系统最新包版本查询 |
| **mcp-server-semgrep** | Szowesgad/mcp-server-semgrep | Semgrep 静态代码安全分析 |

---

## 搜索 & 知识

| 名称 | GitHub | 描述 |
|------|--------|------|
| **brave-search** | modelcontextprotocol/servers/src/brave-search | 官方 Brave Search API |
| **tavily-mcp** | tavily-ai/tavily-mcp | Tavily AI 优化搜索 |
| **exa-mcp-server** | exa-labs/exa-mcp-server | Exa 神经语义搜索 |
| **mcp-server-perplexity** | punkpeye/mcp-server-perplexity | Perplexity AI 搜索 |
| **kagi-mcp** | kagisearch/kagi-mcp-server | Kagi 搜索引擎 |
| **mcp-server-duckduckgo** | nickclyde/duckduckgo-mcp-server | DuckDuckGo 无需 API key |
| **mcp-searxng** | ihor-sokoliuk/mcp-searxng | SearXNG 隐私元搜索 |
| **memory** | modelcontextprotocol/servers/src/memory | 官方知识图谱持久记忆 |
| **mcp-knowledge-graph** | shaneholloman/mcp-knowledge-graph | 持久化知识图谱长期记忆 |
| **context7** | upstash/context7-mcp | **实时库文档查询，强烈推荐** |
| **sequential-thinking** | modelcontextprotocol/servers/src/sequentialthinking | 结构化问题分解推理 |

安装 context7：`npx -y @upstash/context7-mcp`
安装 sequential-thinking：`npx -y @modelcontextprotocol/server-sequential-thinking`

---

## 生产力 & 通讯

| 名称 | GitHub | 描述 |
|------|--------|------|
| **slack** | modelcontextprotocol/servers/src/slack | 官方 Slack 消息和频道 |
| **mcp-server-notion** | makenotion/notion-mcp-server | 官方 Notion 页面和数据库 |
| **linear-mcp** | linear/linear-mcp | Linear 项目管理 |
| **jira-mcp** | KS-GEN-AI/jira-mcp-server | Jira issue 管理 |
| **confluence-mcp** | KS-GEN-AI/confluence-mcp-server | Confluence wiki |
| **mcp-gmail** | gongrzhe/Gmail-MCP-Server | Gmail 读写搜索 |
| **mcp-google-calendar** | nspady/google-calendar-mcp | Google 日历管理 |
| **mcp-server-discord** | v-3/discordmcp | Discord 消息集成 |
| **mcp-telegram** | chigwell/telegram-mcp | Telegram 消息 |
| **mcp-server-todoist** | abhiz123/todoist-mcp-server | Todoist 任务管理 |
| **mcp-trello** | delorenj/mcp-server-trello | Trello 看板管理 |

---

## AI & LLM 工具

| 名称 | GitHub | 描述 |
|------|--------|------|
| **mcp-openai** | mzxrai/mcp-openai | OpenAI API 集成 |
| **mcp-chroma** | chroma-core/chroma-mcp | ChromaDB 向量存储 RAG |
| **mcp-pinecone** | pinecone-io/pinecone-mcp | Pinecone 向量数据库 |
| **mcp-server-llamacloud** | run-llama/mcp-server-llamacloud | LlamaCloud RAG 管道 |
| **mcp-huggingface** | evalstate/mcp-hfspace | HuggingFace 模型和 Spaces |

---

## 监控 & 可观测性

| 名称 | GitHub | 描述 |
|------|--------|------|
| **mcp-server-datadog** | GeLi2001/datadog-mcp-server | Datadog 指标/日志/监控 |
| **mcp-grafana** | grafana/mcp-grafana | Grafana 仪表盘 |
| **mcp-server-prometheus** | pab1it0/prometheus-mcp-server | Prometheus PromQL 查询 |
| **mcp-sentry** | modelcontextprotocol/servers/src/sentry | 官方 Sentry 错误追踪 |
| **mcp-server-newrelic** | newrelic/newrelic-mcp-server | New Relic 可观测性 |
| **mcp-pagerduty** | wpfleger96/pagerduty-mcp-server | PagerDuty 事件管理 |

---

## 文档处理

| 名称 | GitHub | 描述 |
|------|--------|------|
| **mcp-server-markitdown** | KorigamiK/markitdown-mcp-server | Office/PDF/HTML 转 Markdown |
| **mcp-server-pandoc** | vivekVells/mcp-pandoc | Pandoc 文档格式转换 |
| **mcp-pdf-reader** | sylphlab/pdf-reader-mcp | PDF 文本和元数据提取 |

---

## 地图 & 数据

| 名称 | GitHub | 描述 |
|------|--------|------|
| **google-maps** | modelcontextprotocol/servers/src/google-maps | 官方 Google Maps 地理编码/路线/地点 |
| **mcp-server-weather** | adhikasp/mcp-weather | Open-Meteo 天气数据 |

---

## 金融 & 电商

| 名称 | GitHub | 描述 |
|------|--------|------|
| **mcp-stripe** | stripe/agent-toolkit | Stripe 支付管理 |
| **mcp-coinbase** | coinbase/agentkit | Coinbase 加密货币操作 |
| **mcp-shopify** | rezajafari/mcp-server-shopify | Shopify 店铺管理 |

---

## 安装方法（Claude Code）

```bash
# 添加 MCP 到当前项目
claude mcp add <name> -- npx -y <package>

# 添加到全局（所有项目可用）
claude mcp add --scope user <name> -- npx -y <package>

# 示例
claude mcp add playwright -- npx @playwright/mcp@latest
claude mcp add context7 -- npx -y @upstash/context7-mcp
claude mcp add github -- npx -y @modelcontextprotocol/server-github
claude mcp add memory -- npx -y @modelcontextprotocol/server-memory
claude mcp add brave-search -- npx -y @modelcontextprotocol/server-brave-search
claude mcp add filesystem -- npx -y @modelcontextprotocol/server-filesystem /path
claude mcp add sequential-thinking -- npx -y @modelcontextprotocol/server-sequential-thinking

# 查看/管理
claude mcp list
claude mcp remove <name>
```

---

## 开发者推荐必装 TOP 8

1. **playwright-mcp** - 浏览器自动化
2. **context7** - 实时查询最新库文档
3. **github** - GitHub 操作
4. **memory** - 持久化记忆
5. **brave-search 或 tavily** - 网络搜索
6. **sequential-thinking** - 复杂推理
7. **filesystem** - 扩展文件访问
8. **sqlite/postgres** - 数据库查询
