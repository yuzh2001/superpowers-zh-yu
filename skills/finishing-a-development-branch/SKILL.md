---
name: finishing-a-development-branch
description: 当实施完成、所有测试通过，需要决定如何整合工作时使用 - 通过呈现结构化选项来引导完成开发工作，包括合并、PR 或清理
---

# 完成开发分支

## 概述

通过呈现清晰选项和处理所选工作流来引导完成开发工作。

**核心原则：** 验证测试 → 呈现选项 → 执行选择 → 清理。

**开始时宣布：** "我正在使用 finishing-a-development-branch 技能来完成这项工作。"

## 流程

### 第 1 步：验证测试

**呈现选项前，验证测试通过：**

```bash
# 运行项目的测试套件
npm test / cargo test / pytest / go test ./...
```

**如果测试失败：**
```
测试失败（<N> 个失败）。完成前必须修复：

[展示失败]

测试通过前不能继续合并/PR。
```

停止。不要继续第 2 步。

**如果测试通过：** 继续第 2 步。

### 第 2 步：确定基础分支

```bash
# 尝试常见的基础分支
git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null
```

或询问："这个分支从 main 分出来 - 对吗？"

### 第 3 步：呈现选项

精确呈现这 4 个选项：

```
实施完成。你想做什么？

1. 本地合并回 <base-branch>
2. 推送并创建 Pull Request
3. 保持分支原样（我稍后处理）
4. 丢弃这项工作

选哪个？
```

**别加解释** - 保持选项简洁。

### 第 4 步：执行选择

#### 选项 1：本地合并

```bash
# 切换到基础分支
git checkout <base-branch>

# 拉取最新
git pull

# 合并功能分支
git merge <feature-branch>

# 验证合并结果的测试
<test command>

# 如果测试通过
git branch -d <feature-branch>
```

然后：清理 worktree（第 5 步）

#### 选项 2：推送并创建 PR

```bash
# 推送分支
git push -u origin <feature-branch>

# 创建 PR
gh pr create --title "<title>" --body "$(cat <<'EOF'
## 摘要
<2-3 点改动说明>

## 测试计划
- [ ] <验证步骤>
EOF
)"
```

然后：清理 worktree（第 5 步）

#### 选项 3：保持原样

报告："保持分支 <name>。Worktree 保留在 <path>。"

**别清理 worktree。**

#### 选项 4：丢弃

**先确认：**
```
这将永久删除：
- 分支 <name>
- 所有提交：<commit-list>
- Worktree 位于 <path>

输入 'discard' 确认。
```

等待精确确认。

如果确认：
```bash
git checkout <base-branch>
git branch -D <feature-branch>
```

然后：清理 worktree（第 5 步）

### 第 5 步：清理 Worktree

**对选项 1、2、4：**

检查是否在 worktree 中：
```bash
git worktree list | grep $(git branch --show-current)
```

如果是：
```bash
git worktree remove <worktree-path>
```

**对选项 3：** 保留 worktree。

## 快速参考

| 选项 | 合并 | 推送 | 保留 Worktree | 清理分支 |
|------|------|------|---------------|----------|
| 1. 本地合并 | ✓ | - | - | ✓ |
| 2. 创建 PR | - | ✓ | ✓ | - |
| 3. 保持原样 | - | - | ✓ | - |
| 4. 丢弃 | - | - | - | ✓ (强制) |

## 常见错误

**跳过测试验证**
- **问题：** 合并坏代码，创建失败的 PR
- **修复：** 总是在提供选项前验证测试

**开放式问题**
- **问题：** "接下来该做什么？" → 模糊不清
- **修复：** 精确呈现 4 个结构化选项

**自动清理 worktree**
- **问题：** 可能还需要时移除 worktree（选项 2、3）
- **修复：** 只在选项 1 和 4 时清理

**丢弃时没有确认**
- **问题：** 意外删除工作
- **修复：** 要求输入 "discard" 确认

## 红旗

**永远不要：**
- 测试失败时继续
- 合并时不验证结果的测试
- 没有确认就删除工作
- 没有明确请求就强制推送

**总是：**
- 提供选项前验证测试
- 精确呈现 4 个选项
- 选项 4 时获取输入确认
- 只在选项 1 和 4 时清理 worktree

## 集成

**被调用：**
- **subagent-driven-development**（第 7 步）- 所有任务完成后
- **executing-plans**（第 5 步）- 所有批次完成后

**配合使用：**
- **using-git-worktrees** - 清理该技能创建的 worktree
