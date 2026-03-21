---
name: mcp-builder
description: Build MCP (Model Context Protocol) servers with Node.js/TypeScript SDK. Tools, resources, prompts, Zod validation, stdio transport. Use when creating or maintaining MCP servers.
argument-hint: [MCP server name or feature]
allowed-tools: Read, Write, Edit, Bash, Glob
---

# MCP Server Builder

## 快速起步

```bash
mkdir my-mcp-server && cd my-mcp-server
npm init -y
npm install @modelcontextprotocol/sdk zod
npm install -D typescript @types/node ts-node
```

## 最小 MCP 服务器

```typescript
// src/index.ts
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js'
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js'
import { z } from 'zod'

const server = new McpServer({
  name: 'my-server',
  version: '1.0.0',
})

// 注册工具
server.tool(
  'get_weather',
  '获取城市天气',
  { city: z.string().describe('城市名称') },
  async ({ city }) => {
    const data = await fetchWeather(city)
    return {
      content: [{ type: 'text', text: JSON.stringify(data) }]
    }
  }
)

// 注册资源
server.resource(
  'config',
  'config://app',
  { mimeType: 'application/json' },
  async () => ({
    contents: [{ uri: 'config://app', text: JSON.stringify(config) }]
  })
)

// 启动
const transport = new StdioServerTransport()
await server.connect(transport)
```

## 工具设计原则
1. **单一职责** — 每个工具做一件事
2. **清晰描述** — description 要具体，AI 依赖它判断何时调用
3. **Zod 验证** — 所有参数用 Zod schema 定义
4. **错误处理** — 返回有意义的错误信息
5. **幂等性** — 只读工具明确标注 `readOnly: true`

## 错误处理
```typescript
server.tool('risky_op', '...', { input: z.string() }, async ({ input }) => {
  try {
    const result = await doSomething(input)
    return { content: [{ type: 'text', text: result }] }
  } catch (error) {
    return {
      content: [{ type: 'text', text: `错误: ${error.message}` }],
      isError: true
    }
  }
})
```

## 注册到 Claude Code（settings.json）
```json
{
  "mcpServers": {
    "my-server": {
      "command": "node",
      "args": ["C:/path/to/my-mcp-server/dist/index.js"]
    }
  }
}
```

## 调试
```bash
# 用 MCP Inspector 测试
npx @modelcontextprotocol/inspector node dist/index.js
```
