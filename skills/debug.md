---
description: Diagnose and fix bugs, errors, crashes, or unexpected behavior. Use when user reports an error, exception, or broken functionality.
argument-hint: [error message or description]
allowed-tools: Read, Bash, Edit, Grep, Glob
---

# Debug

## 诊断流程
1. **理解症状** — 明确错误信息、复现步骤、预期 vs 实际行为
2. **定位根因** — 追踪调用栈，找到真正出错的位置（不要修表面）
3. **假设 → 验证** — 提出 1-2 个假设，最小化代码验证
4. **最小化修复** — 只改必要的，不顺手重构
5. **验证修复** — 确保 fix 不破坏其他功能

## 常见根因检查清单
- [ ] 空值/undefined 解引用
- [ ] 类型不匹配（隐式转换）
- [ ] 异步竞态条件（race condition）
- [ ] 依赖版本冲突
- [ ] 环境变量缺失
- [ ] 编码/解码问题（UTF-8 等）
- [ ] 边界条件（数组越界、整数溢出）
- [ ] 资源泄漏（未关闭连接/文件）

## 输出格式
```
根因: <一句话说明>
位置: file:line
修复: <最小改动说明>
```

先读相关文件，再动手修改。
