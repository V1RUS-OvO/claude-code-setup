---
name: refactor
description: Refactor code for better readability, maintainability, and performance without changing behavior. Use when user asks to clean up, improve, or restructure code.
argument-hint: [file or component to refactor]
allowed-tools: Read, Edit, Grep, Glob, Bash
---

# Refactor

## 重构原则
- **不改变行为** — 重构前后功能完全一致
- **有测试保护** — 没有测试先写测试，再重构
- **小步前进** — 每次只做一种改动，保持可回滚
- **按需重构** — 只改当前任务涉及的代码

## 识别重构信号
- 函数超过 30 行
- 嵌套超过 3 层
- 同样的逻辑出现 3 次以上（Rule of Three）
- 命名不清晰（temp, data, obj, flag）
- 函数做了不止一件事
- 注释在解释「是什么」而非「为什么」

## 常用重构手法

### Extract Function（提取函数）
```typescript
// Before
function processOrder(order) {
  // 计算折扣
  let discount = 0
  if (order.total > 100) discount = 0.1
  if (order.isVip) discount += 0.05
  const final = order.total * (1 - discount)
  // 发送邮件...
}

// After
function calculateDiscount(order) {
  let discount = 0
  if (order.total > 100) discount = 0.1
  if (order.isVip) discount += 0.05
  return discount
}
function processOrder(order) {
  const final = order.total * (1 - calculateDiscount(order))
  // 发送邮件...
}
```

### Early Return（提前返回，减少嵌套）
```typescript
// Before
function getDiscount(user) {
  if (user) {
    if (user.isVip) {
      if (user.years > 3) { return 0.2 }
      else { return 0.1 }
    } else { return 0 }
  } else { return 0 }
}

// After
function getDiscount(user) {
  if (!user) return 0
  if (!user.isVip) return 0
  return user.years > 3 ? 0.2 : 0.1
}
```

### Replace Magic Numbers
```typescript
// Before
if (status === 3) { ... }
// After
const ORDER_STATUS = { SHIPPED: 3 } as const
if (status === ORDER_STATUS.SHIPPED) { ... }
```

## 重构流程
1. 确认有测试覆盖（或先写测试）
2. 运行测试，确认全部通过
3. 做一个小重构
4. 再次运行测试
5. 重复直到满意
6. 不要在重构中顺便加功能
