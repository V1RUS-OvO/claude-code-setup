---
name: explain
description: Explain code, architecture, algorithms, or concepts clearly. Use when user asks what code does, how something works, or wants to understand unfamiliar patterns.
argument-hint: [code, file, or concept to explain]
allowed-tools: Read, Grep, Glob
---

# Code Explanation

## 解释层次（根据用户背景调整）

### 快速概览（默认）
1. **这是什么** — 一句话功能定位
2. **关键部分** — 最重要的 3-5 个部分
3. **数据流** — 输入 → 处理 → 输出
4. **注意事项** — 副作用、性能、限制

### 深度解释（用户要求时）
- 逐行注释关键逻辑
- 解释设计决策（为什么这样写）
- 说明替代方案
- 指出潜在问题

## 解释框架

### 解释函数
```
功能: [一句话]
参数: [每个参数的含义]
返回: [返回值含义]
副作用: [是否修改外部状态、IO操作]
注意: [边界条件、已知限制]
```

### 解释架构
```
整体目标: [解决什么问题]
主要组件:
  - [组件A]: [职责]
  - [组件B]: [职责]
数据流: A → B → C
关键依赖: [重要库/服务]
```

### 解释算法
```
算法: [名称]
时间复杂度: O(?)
空间复杂度: O(?)
核心思路: [用大白话描述]
示例: 输入 [X] → 步骤 → 输出 [Y]
```

## 输出原则
- 用中文解释，代码保持原语言
- 先说「是什么」，再说「怎么做」
- 用具体例子辅助说明
- 不假设用户知道术语，遇到专业词汇简短解释
- 控制长度，不啰嗦
