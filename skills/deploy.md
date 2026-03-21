---
name: deploy
description: CI/CD pipeline setup, Docker deployment, health checks, rollback strategies, blue-green/canary deployments, production readiness checklist. Use when setting up pipelines or planning production releases.
argument-hint: [platform: github-actions/gitlab/vercel/fly/k8s]
allowed-tools: Read, Write, Edit, Bash, Glob
---

# Deployment Patterns

## 部署策略选择

| 策略 | 停机时间 | 风险 | 适用场景 |
|------|---------|------|----------|
| Rolling | 无 | 中 | 大多数 web 应用 |
| Blue-Green | 无 | 低 | 需要即时回滚 |
| Canary | 无 | 最低 | 高流量，需要灰度 |
| Recreate | 有 | 高 | 开发环境、低流量 |

## GitHub Actions CI/CD

```yaml
# .github/workflows/deploy.yml
name: Deploy
on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '20', cache: 'npm' }
      - run: npm ci
      - run: npm test
      - run: npm run build

  deploy:
    needs: test
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Fly.io
        run: flyctl deploy --remote-only
        env:
          FLY_API_TOKEN: ${{ secrets.FLY_API_TOKEN }}
```

## 健康检查

```typescript
// GET /health
app.get('/health', async (req, res) => {
  const checks = {
    status: 'ok',
    timestamp: new Date().toISOString(),
    version: process.env.APP_VERSION,
    checks: {
      database: await checkDatabase(),
      redis: await checkRedis(),
    }
  }
  const healthy = Object.values(checks.checks).every(c => c === 'ok')
  res.status(healthy ? 200 : 503).json(checks)
})
```

## 生产发布清单
- [ ] 所有测试通过（unit + integration + e2e）
- [ ] 数据库 migration 已准备（可回滚）
- [ ] 环境变量已配置（staging 验证过）
- [ ] 健康检查端点正常
- [ ] 错误监控已接入（Sentry 等）
- [ ] 回滚计划已确认
- [ ] 告警规则已设置
- [ ] 发布时间选在低峰期

## 回滚
```bash
# Docker
docker service update --rollback myapp
# Kubernetes
kubectl rollout undo deployment/myapp
# Fly.io
flyctl releases list && flyctl deploy --image registry.fly.io/app:v42
# Vercel
vercel rollback [deployment-url]
```

## 环境变量管理
```bash
# 永远不要在代码中硬编码
# 使用平台 secrets：
# GitHub: Settings → Secrets and variables → Actions
# Fly.io: flyctl secrets set KEY=VALUE
# Vercel: vercel env add
# Render: Dashboard → Environment
```
