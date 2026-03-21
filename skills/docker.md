---
name: docker
description: Docker and Docker Compose patterns for local dev, container security, networking, volumes, multi-service orchestration. Use when setting up Docker, writing Dockerfiles, or debugging containers.
argument-hint: [service or app to containerize]
allowed-tools: Read, Write, Edit, Bash, Glob
---

# Docker Patterns

## 标准 Web App 开发环境

```yaml
# docker-compose.yml
services:
  app:
    build:
      context: .
      target: dev
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules  # 防止宿主机覆盖容器依赖
    environment:
      - NODE_ENV=development
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

## 多阶段 Dockerfile（安全 + 小体积）

```dockerfile
# 构建阶段
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

# 开发阶段
FROM node:20-alpine AS dev
WORKDIR /app
COPY package*.json ./
RUN npm ci
CMD ["npm", "run", "dev"]

# 生产阶段（最小镜像）
FROM node:20-alpine AS prod
WORKDIR /app
# 非 root 用户运行
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
COPY --from=builder --chown=appuser:appgroup /app/dist ./dist
COPY --from=builder --chown=appuser:appgroup /app/node_modules ./node_modules
USER appuser
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

## 常用命令
```bash
# 启动
docker compose up -d
# 查看日志
docker compose logs -f app
# 进入容器
docker compose exec app sh
# 重建
docker compose build --no-cache app
# 清理
docker compose down -v  # 删除 volumes
```

## 安全最佳实践
- [ ] 使用非 root 用户运行（USER appuser）
- [ ] 多阶段构建，生产镜像不含 devDependencies
- [ ] 使用 .dockerignore 排除 .env、node_modules、.git
- [ ] 固定镜像版本（不用 latest）
- [ ] secrets 用环境变量注入，不 COPY 到镜像
- [ ] 定期扫描镜像漏洞：`docker scout cves image:tag`
