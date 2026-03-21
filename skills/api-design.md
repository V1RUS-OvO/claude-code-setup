---
name: api-design
description: REST API design patterns: resource naming, HTTP status codes, pagination, filtering, error responses, versioning, rate limiting. Use when designing or reviewing API endpoints.
argument-hint: [API resource or feature]
allowed-tools: Read, Write, Edit, Grep, Glob
---

# API Design Patterns

## URL 结构
```
# 名词复数，kebab-case
GET    /api/v1/users
GET    /api/v1/users/:id
POST   /api/v1/users
PUT    /api/v1/users/:id
PATCH  /api/v1/users/:id
DELETE /api/v1/users/:id

# 子资源
GET    /api/v1/users/:id/orders

# 动作（谨慎使用动词）
POST   /api/v1/orders/:id/cancel
POST   /api/v1/auth/refresh
```

## HTTP 状态码
```
200 OK           — GET/PUT/PATCH 成功
201 Created      — POST 创建成功（附 Location header）
204 No Content   — DELETE 成功
400 Bad Request  — 校验失败、格式错误
401 Unauthorized — 未认证
403 Forbidden    — 已认证但无权限
404 Not Found    — 资源不存在
409 Conflict     — 重复创建、状态冲突
422 Unprocessable — 语义无效
429 Too Many Requests — 限流
500 Internal Error — 服务器异常（不暴露细节）
```

## 响应格式

### 单资源
```json
{ "data": { "id": "abc", "email": "user@example.com", "created_at": "2025-01-15T10:30:00Z" } }
```

### 集合（带分页）
```json
{
  "data": [{"id": "abc"}, {"id": "def"}],
  "meta": { "total": 142, "page": 1, "per_page": 20, "total_pages": 8 },
  "links": { "next": "/api/v1/users?page=2", "last": "/api/v1/users?page=8" }
}
```

### 错误响应
```json
{
  "error": {
    "code": "validation_error",
    "message": "请求验证失败",
    "details": [
      { "field": "email", "message": "邮箱格式无效" }
    ]
  }
}
```

## 过滤 / 排序 / 分页
```
GET /api/v1/users?status=active&role=admin    # 过滤
GET /api/v1/users?sort=created_at&order=desc  # 排序
GET /api/v1/users?page=2&per_page=20          # 分页
GET /api/v1/users?fields=id,email,name        # 字段选择
```

## 版本控制
```
# URL 版本（推荐）
/api/v1/users
/api/v2/users

# Header 版本
API-Version: 2025-01
```

## 常见错误
```
# ❌ 所有响应返回 200
{ "status": 200, "success": false, "error": "Not found" }

# ✅ 使用语义化 HTTP 状态码
HTTP 404
{ "error": { "code": "not_found", "message": "用户不存在" } }
```
