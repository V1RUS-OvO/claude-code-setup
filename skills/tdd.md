---
name: tdd
description: Test-driven development workflow. Write tests first, then implement. Use when writing new features, fixing bugs, or refactoring. Enforces 80%+ coverage.
argument-hint: [feature or function to implement]
allowed-tools: Read, Write, Edit, Bash, Grep, Glob
---

# TDD Workflow

**原则：先写测试，再写实现。**

## 流程（Red → Green → Refactor）

### Step 1: 理解需求，写用户故事
```
作为 [角色]，我想要 [功能]，以便 [价值]
```

### Step 2: 生成测试用例（先不实现）
```typescript
describe('功能名', () => {
  it('should 正常行为 when 正常条件', async () => { ... })
  it('should 处理空值 when 输入为空', async () => { ... })
  it('should 抛出错误 when 无效输入', async () => { ... })
  it('should 降级处理 when 外部服务不可用', async () => { ... })
})
```

覆盖矩阵：
- Happy path（正常流程）
- 边界条件（空、零、最大值）
- 错误路径（无效输入、依赖失败）
- 异步行为（rejection、超时）

### Step 3: 运行测试 → 确认失败（Red）
```bash
npm test  # 应该失败
```

### Step 4: 写最小实现让测试通过（Green）
只写让测试通过的最少代码，不过度设计。

### Step 5: 再次运行测试 → 确认通过
```bash
npm test  # 应该通过
```

### Step 6: 重构（保持测试绿色）
- 消除重复
- 改善命名
- 优化性能
- 提升可读性

### Step 7: 检查覆盖率
```bash
npm run test:coverage  # 目标 80%+
```

## Mock 策略
```typescript
// HTTP 请求
jest.mock('axios')
// 数据库：事务回滚
beforeEach(() => db.beginTransaction())
afterEach(() => db.rollback())
// 时间
jest.useFakeTimers()
// 文件系统
jest.mock('fs/promises')
```

## 命名规范
```
describe('ClassName') → describe('methodName') → it('should X when Y')
```
