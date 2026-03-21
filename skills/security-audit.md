---
name: security-audit
description: Security review for authentication, user input handling, secrets management, API endpoints, payment features. Use when implementing auth, handling uploads, creating APIs, or working with sensitive data.
argument-hint: [file or feature to audit]
allowed-tools: Read, Grep, Glob, Bash
---

# Security Audit

## 激活时机
- 实现认证/授权功能
- 处理用户输入或文件上传
- 创建新 API 端点
- 使用 secrets 或凭据
- 处理支付或敏感数据

## 审计清单

### 1. Secrets 管理
```typescript
// ❌ 禁止硬编码
const apiKey = "sk-xxx"
// ✅ 必须从环境变量读取
const apiKey = process.env.API_KEY
if (!apiKey) throw new Error('API_KEY not configured')
```
- [ ] 无硬编码 key/密码/token
- [ ] 所有 secrets 在环境变量中
- [ ] .env 文件在 .gitignore 中
- [ ] git history 无 secrets 泄露

### 2. 输入验证
```typescript
// 使用 Zod 等 schema 验证所有用户输入
const schema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(100)
})
```
- [ ] 所有用户输入用 schema 验证
- [ ] 文件上传限制（大小、类型、扩展名）
- [ ] 使用白名单而非黑名单
- [ ] 错误信息不泄露系统细节

### 3. SQL 注入防护
```typescript
// ❌ 禁止字符串拼接
const q = `SELECT * FROM users WHERE id = ${userId}`
// ✅ 使用参数化查询
const user = await db.query('SELECT * FROM users WHERE id = $1', [userId])
```

### 4. XSS 防护
- [ ] 输出到 HTML 时转义用户内容
- [ ] 使用框架的安全渲染（React JSX 自动转义）
- [ ] CSP header 已配置
- [ ] dangerouslySetInnerHTML 仅用于受信内容

### 5. 认证 & 授权
- [ ] 每个受保护端点都验证 token
- [ ] 水平权限检查（用户只能访问自己的资源）
- [ ] JWT 有过期时间
- [ ] 密码用 bcrypt/argon2 哈希（不用 MD5/SHA1）

### 6. 依赖安全
```bash
npm audit
# 或
pip-audit
```
- [ ] 无已知高危漏洞的依赖
- [ ] 锁定依赖版本

## 报告格式
```
🔴 Critical: [具体问题] @ file:line → [修复方法]
🟠 High: ...
🟡 Medium: ...
🔵 Low: ...
💡 建议: ...
```
