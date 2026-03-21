---
name: db-migration
description: Database migration best practices for schema changes, data migrations, rollbacks, zero-downtime deployments. Use when creating/altering tables, adding indexes, or planning schema changes.
argument-hint: [schema change description]
allowed-tools: Read, Write, Edit, Bash, Grep
---

# Database Migration Patterns

## 核心原则
1. **所有变更都是 migration** — 永远不要手动修改生产数据库
2. **生产环境只前进** — 回滚用新的前向 migration
3. **先兼容，后清理** — 添加新列/表，确保兼容旧代码，再删旧结构
4. **数据 migration 单独进行** — 不在 schema migration 中大量更新数据

## 常用工具

### Prisma
```bash
npx prisma migrate dev --name add_user_avatar  # 开发
npx prisma migrate deploy                        # 生产
npx prisma migrate status                        # 查看状态
```

### Drizzle
```bash
npx drizzle-kit generate
npx drizzle-kit migrate
```

### golang-migrate
```bash
migrate -path ./migrations -database $DATABASE_URL up
migrate -path ./migrations -database $DATABASE_URL down 1
```

## 安全变更模式

### 添加非空列（零停机）
```sql
-- 步骤 1: 添加可空列（不影响现有数据）
ALTER TABLE users ADD COLUMN avatar_url TEXT;

-- 步骤 2: 部署新代码（写入 avatar_url）

-- 步骤 3: 回填历史数据（分批，避免锁表）
UPDATE users SET avatar_url = '' WHERE avatar_url IS NULL AND id BETWEEN 1 AND 10000;

-- 步骤 4: 添加 NOT NULL 约束（确认无 NULL 后）
ALTER TABLE users ALTER COLUMN avatar_url SET NOT NULL;
```

### 重命名列（零停机）
```sql
-- ❌ 不要直接重命名（会破坏旧代码）
-- ✅ 分步骤：
-- 1. 添加新列
ALTER TABLE users ADD COLUMN full_name TEXT;
-- 2. 双写（新旧列都写）
-- 3. 回填：UPDATE users SET full_name = name;
-- 4. 切换到只读新列
-- 5. 删除旧列（下次部署）
```

### 添加索引（不锁表）
```sql
-- PostgreSQL: CONCURRENTLY 不锁表
CREATE INDEX CONCURRENTLY idx_users_email ON users(email);
-- MySQL: ALGORITHM=INPLACE, LOCK=NONE
CREATE INDEX idx_users_email ON users(email) ALGORITHM=INPLACE, LOCK=NONE;
```

## 危险操作清单
```sql
-- ⚠️ 会锁表的操作（生产慎用）:
ALTER TABLE ... ADD COLUMN ... NOT NULL DEFAULT ...  -- PostgreSQL < 11
DROP TABLE ...
TRUNCATE TABLE ...
ALTER TABLE ... RENAME COLUMN ...
```

## Migration 文件命名
```
20250115_001_add_users_table.sql
20250115_002_add_email_index.sql
20250116_001_add_avatar_column.sql
```
