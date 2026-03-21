---
name: context7-lookup
description: Look up latest documentation for any library or framework using Context7 MCP. Use when working with a specific library and need current API docs, usage examples, or version-specific information.
argument-hint: [library name and topic]
allowed-tools: Bash, Read
---

# Context7 Documentation Lookup

## 使用场景
- 需要某个库的最新 API 文档
- 不确定某个函数的参数或返回值
- 需要库的使用示例
- 库版本升级，需要查看 breaking changes

## 使用方法

在提问时加上 `use context7` 指令，Context7 MCP 会自动查询最新文档：

**示例:**
```
如何在 Next.js 15 中配置 middleware？use context7

Prisma 如何做 upsert 操作？use context7

React 19 的 use() hook 怎么用？use context7
```

## Context7 MCP 工具

Context7 提供两个核心工具：

1. **resolve-library-id** — 将库名解析为 Context7 ID
   - 输入: `react`, `nextjs`, `prisma`, `fastapi` 等
   - 输出: Context7 库 ID

2. **get-library-docs** — 获取指定库的文档
   - 输入: 库 ID + 查询主题 + token 数量
   - 输出: 最新官方文档内容

## 支持的常用库
- React, Next.js, Vue, Nuxt, Svelte
- Prisma, Drizzle, TypeORM
- Express, Fastify, Hono
- FastAPI, Django, Flask
- Tailwind CSS, shadcn/ui
- Vitest, Jest, Playwright
- tRPC, GraphQL, Zod
- Docker, Kubernetes

## 提示
- 查询越具体越好（「Next.js App Router 动态路由」比「Next.js」好）
- 文档较长时，指定 topic 参数聚焦到具体功能
- 配合 sequential-thinking MCP 做复杂技术决策
