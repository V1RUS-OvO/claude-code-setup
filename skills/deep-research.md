---
name: deep-research
description: Multi-source deep research using available MCP tools (fetch, duckduckgo, context7). Use when user asks to research, investigate, deep dive, or find current information on any topic.
argument-hint: [research topic]
allowed-tools: Bash, Read, WebFetch, WebSearch
---

# Deep Research

使用可用的 MCP 工具（fetch、duckduckgo、context7）对任意主题进行多源深度研究，输出带引用的综合报告。

## 激活时机
- 用户说「研究一下」「深入了解」「调查」「找资料」
- 竞品分析、技术选型、市场调研
- 需要综合多个来源的信息

## 研究流程

### Step 1: 拆解子问题
将主题拆成 3-5 个子问题，明确研究目标。

### Step 2: 多源搜索
对每个子问题，使用不同关键词变体搜索 2-3 次：
- 优先使用 duckduckgo MCP 搜索
- 用 fetch MCP 读取关键页面全文
- 用 context7 MCP 查询技术文档
- 目标：15-30 个独立来源

### Step 3: 深读关键来源
对最重要的 3-5 个 URL 获取完整内容，不依赖摘要片段。

### Step 4: 综合撰写报告

```markdown
# [主题]: 研究报告
*生成时间: [日期] | 来源: [N]个 | 置信度: 高/中/低*

## 执行摘要
[3-5 句核心发现]

## 1. [第一主题]
[发现 + 行内引用]
- 关键点 ([来源名](url))

## 2. [第二主题]
...

## 关键结论
- [可操作的洞察]

## 来源列表
1. [来源名](url) — 相关性说明
```

## 质量标准
- 每个重要主张必须有来源
- 区分事实 vs 观点
- 标注信息时效性
- 识别信息缺口
