---
description: Generate comprehensive tests for code. Use when user asks to write tests, add test coverage, or create unit/integration/e2e tests.
argument-hint: [file, function, or feature to test]
allowed-tools: Read, Write, Edit, Bash, Grep, Glob
---

# Test Generation

## 流程
1. 读取目标代码，理解接口和行为
2. 识别项目使用的测试框架（Jest/Vitest/pytest/Go test 等）
3. 按现有测试风格生成

## 覆盖矩阵
- **Happy path** — 正常输入，预期输出
- **边界条件** — 空值、零、最大值、最小值、空数组
- **错误路径** — 无效输入、外部依赖失败
- **异步行为** — Promise rejection、超时
- **副作用** — 数据库写入、API 调用（需 mock）

## Mock 策略
- 外部 HTTP 请求：mock/spy
- 数据库：in-memory 或 transaction rollback
- 时间相关：fake timers
- 文件系统：mock fs

## 命名规范
```
describe('ModuleName', () => {
  describe('methodName', () => {
    it('should <expected behavior> when <condition>', ...)
  })
})
```

优先用项目已有的测试作为参考风格，不要引入新依赖。
