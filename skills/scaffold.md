---
name: scaffold
description: Scaffold new projects, features, or components with proper structure. Use when user wants to create a new project, add a new feature module, or generate boilerplate code.
argument-hint: [project type: nextjs/express/python-api/cli/mcp]
allowed-tools: Read, Write, Edit, Bash, Glob
---

# Project Scaffold

## 快速脚手架命令

### Next.js (App Router)
```bash
npx create-next-app@latest my-app --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"
```

### Express + TypeScript API
```bash
mkdir my-api && cd my-api
npm init -y
npm install express cors helmet morgan dotenv
npm install -D typescript @types/node @types/express ts-node-dev
```

结构:
```
src/
  routes/        # 路由定义
  controllers/   # 请求处理
  services/      # 业务逻辑
  middleware/    # 中间件
  models/        # 数据模型
  utils/         # 工具函数
  index.ts       # 入口
```

### Python FastAPI
```bash
uv init my-api && cd my-api
uv add fastapi uvicorn pydantic-settings
```

结构:
```
app/
  routers/       # 路由
  models/        # Pydantic 模型
  services/      # 业务逻辑
  core/          # 配置、依赖注入
  main.py        # 入口
```

### CLI 工具 (Node.js)
```bash
mkdir my-cli && cd my-cli
npm init -y
npm install commander chalk ora
npm install -D typescript
```

### MCP Server
```bash
mkdir my-mcp && cd my-mcp
npm init -y
npm install @modelcontextprotocol/sdk zod
npm install -D typescript @types/node
```

## 通用文件清单
每个项目都应包含:
- [ ] `README.md` — 项目说明、安装、使用
- [ ] `.env.example` — 所需环境变量（无真实值）
- [ ] `.gitignore` — 忽略 node_modules、.env、dist
- [ ] `tsconfig.json` / `pyproject.toml` — 语言配置
- [ ] 基本 CI（`.github/workflows/ci.yml`）

## 生成时的原则
- 用项目现有的技术栈和风格
- 不引入不必要的依赖
- 文件结构遵循该框架的约定
- 生成后列出需要手动配置的项目
