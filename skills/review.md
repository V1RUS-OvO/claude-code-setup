---
description: Review code for bugs, security issues, performance problems, and best practices. Use when user asks to review, check, or audit code.
argument-hint: [file or function to review]
allowed-tools: Read, Grep, Glob
---

# Code Review

## 审查维度（按优先级）

### 1. 正确性
- 逻辑是否正确，边界条件是否处理
- 错误处理是否完整
- 并发安全（race conditions, deadlocks）

### 2. 安全性（OWASP Top 10）
- 输入验证 / 注入攻击（SQL、XSS、命令注入）
- 认证 & 授权漏洞
- 敏感数据暴露（hardcoded secrets、日志泄露）
- 不安全的反序列化
- 路径遍历

### 3. 性能
- N+1 查询
- 不必要的大对象复制
- 阻塞主线程的操作
- 内存泄漏

### 4. 可维护性
- 函数单一职责
- 命名清晰度
- 魔法数字/字符串
- 重复代码（DRY 原则）

### 5. 测试覆盖
- 关键路径是否有测试
- 边界条件测试

## 输出格式
每个问题按以下格式报告：
```
[严重级别] file:line — 问题描述
建议: 具体改法
```
严重级别: 🔴 Critical / 🟠 High / 🟡 Medium / 🔵 Low / 💡 Suggestion

最后给出总体评价（1-2 句话）。
