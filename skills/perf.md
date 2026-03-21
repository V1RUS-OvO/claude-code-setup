---
name: perf
description: Diagnose and fix performance bottlenecks: N+1 queries, slow renders, memory leaks, bundle size, caching. Use when app is slow, laggy, or has high resource usage.
argument-hint: [area: frontend/backend/database/bundle]
allowed-tools: Read, Edit, Bash, Grep, Glob
---

# Performance Optimization

## 诊断优先于优化
先测量，再优化。不要凭感觉猜瓶颈。

## 后端性能

### N+1 查询（最常见问题）
```typescript
// ❌ N+1: 每个 user 单独查询 orders
const users = await db.users.findMany()
for (const user of users) {
  user.orders = await db.orders.findMany({ where: { userId: user.id } })
}

// ✅ 一次 JOIN 查询
const users = await db.users.findMany({
  include: { orders: true }  // Prisma
})
```

### 数据库索引
```sql
-- 查找慢查询（PostgreSQL）
SELECT query, mean_exec_time, calls
FROM pg_stat_statements
ORDER BY mean_exec_time DESC LIMIT 10;

-- 查找缺失索引
EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 123;
-- 看到 Seq Scan 说明缺索引
CREATE INDEX CONCURRENTLY idx_orders_user_id ON orders(user_id);
```

### 缓存策略
```typescript
// Redis 缓存（read-through 模式）
async function getUser(id: string) {
  const cached = await redis.get(`user:${id}`)
  if (cached) return JSON.parse(cached)

  const user = await db.users.findUnique({ where: { id } })
  await redis.setex(`user:${id}`, 300, JSON.stringify(user))  // 5分钟
  return user
}
```

## 前端性能

### React 渲染优化
```typescript
// 避免不必要重渲染
const MemoComponent = memo(({ data }) => <div>{data}</div>)

// useMemo 缓存昂贵计算
const sorted = useMemo(() => expensiveSort(list), [list])

// useCallback 稳定函数引用
const handleClick = useCallback(() => doSomething(id), [id])

// 虚拟列表（大数据集）
import { VirtualList } from '@tanstack/react-virtual'
```

### Bundle 优化
```bash
# 分析 bundle 大小
npx webpack-bundle-analyzer stats.json
# 或 Next.js
npx @next/bundle-analyzer
```
```typescript
// 动态导入（代码分割）
const HeavyChart = dynamic(() => import('./HeavyChart'), { ssr: false })
// 只在需要时加载大型库
const { jsPDF } = await import('jspdf')
```

## 性能测量工具
```bash
# 后端 API 响应时间
curl -w "\nTotal: %{time_total}s" -o /dev/null -s https://api.example.com/users

# Node.js 性能分析
node --prof app.js
node --prof-process isolate-*.log > profile.txt
```

## 优化优先级
1. 数据库查询（最大收益）
2. 缓存（Redis/CDN）
3. 网络（减少请求数、压缩）
4. 渲染（React 优化）
5. Bundle 大小
