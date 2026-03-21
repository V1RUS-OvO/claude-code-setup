---
name: git-workflow
description: Git workflows, branch strategies, conventional commits, PR templates, conflict resolution, and history cleanup. Use when working with git, branches, commits, or PRs.
argument-hint: [task: commit/branch/pr/rebase/conflict]
allowed-tools: Bash, Read
---

# Git Workflow

## Conventional Commits 规范
```
<type>(<scope>): <subject>

[可选 body]

[可选 footer]
```

类型:
```
feat:     新功能
fix:      修复 bug
docs:     文档变更
style:    格式（不影响逻辑）
refactor: 重构（不是 feat 也不是 fix）
perf:     性能优化
test:     测试相关
chore:    构建/工具链变更
ci:       CI/CD 配置
revert:   回滚
```

示例:
```
feat(auth): 添加微信登录支持
fix(api): 修复分页参数越界导致的 500 错误
perf(db): 为 orders.user_id 添加索引，查询提速 10x
```

## 分支策略（GitHub Flow）
```bash
# 从 main 创建功能分支
git checkout -b feat/user-avatar

# 定期同步 main
git fetch origin && git rebase origin/main

# 完成后合并
git checkout main && git merge --squash feat/user-avatar
git commit -m "feat(user): 添加用户头像功能"
git branch -d feat/user-avatar
```

## 常用操作
```bash
# 撤销最后一次 commit（保留改动）
git reset --soft HEAD~1

# 修改最后一次 commit message
git commit --amend -m "新消息"

# 暂存当前工作
git stash push -m "wip: 进行中的功能"
git stash pop

# 查找引入 bug 的 commit
git bisect start
git bisect bad HEAD
git bisect good v1.0.0

# 挑选特定 commit
git cherry-pick abc1234

# 清理已合并分支
git branch --merged main | grep -v main | xargs git branch -d
```

## 解决冲突
```bash
# rebase 时遇到冲突
git rebase origin/main
# 1. 编辑冲突文件，解决 <<<< ==== >>>> 标记
# 2. git add <resolved-files>
# 3. git rebase --continue
# 4. 如果想放弃: git rebase --abort
```

## .gitignore 常用模式
```gitignore
# 环境变量
.env
.env.local
.env.*.local

# 依赖
node_modules/
__pycache__/
*.pyc

# 构建产物
dist/
build/
.next/

# IDE
.vscode/settings.json
.idea/
*.swp
```
