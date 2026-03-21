---
name: pr-review
description: Review a pull request thoroughly: correctness, security, performance, tests, and style. Use when asked to review a PR or diff.
argument-hint: [PR number or diff]
allowed-tools: Bash, Read, Grep, Glob
---

# PR Review

## 审查流程

### 1. 理解上下文
```bash
# 查看 PR 概述
gh pr view <number>
# 查看改动
gh pr diff <number>
# 查看 CI 状态
gh pr checks <number>
```

### 2. 审查维度（按优先级）

**🔴 必须修改（阻塞合并）**
- 正确性错误（逻辑 bug、数据丢失风险）
- 安全漏洞（注入、越权、secrets 泄露）
- 破坏已有功能的改动
- 测试缺失（关键路径）

**🟠 应该修改（强烈建议）**
- 性能问题（N+1、缺索引）
- 错误处理不完整
- 边界条件未覆盖

**🟡 建议改进（不阻塞）**
- 代码可读性
- 命名改善
- 重复代码

**💡 可选建议**
- 风格偏好
- 替代实现

### 3. 检查清单
- [ ] 功能是否符合 PR 描述？
- [ ] 是否有测试覆盖新功能和边界条件？
- [ ] 是否有安全风险？
- [ ] 数据库变更是否有 migration？
- [ ] 是否更新了相关文档/注释？
- [ ] CI 是否通过？
- [ ] 是否有不必要的改动（格式、无关重构）？

## 评论格式
```
**[严重级别]** `file.ts:line`

问题: 简短描述

建议:
```typescript
// 改为这样
```

原因: 解释为什么这样更好
```

## 整体评价
最后给 1-2 句总体评价：
- 改动质量
- 最重要的待处理项
- 是否 approve / request changes
