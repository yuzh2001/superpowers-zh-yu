---
name: writing-plans
description: 当你有规格或需求要做多步骤任务时使用，在动代码之前
---

# 编写计划

## 概述

假设工程师对我们的代码库零上下文且品味可疑，编写全面的实施计划。记录他们需要知道的一切：每个任务要动哪些文件、代码、测试、可能需要查的文档、如何测试。把整个计划分成小任务。DRY。YAGNI。TDD。频繁提交。

假设他们是熟练的开发者，但对我们的工具集或问题域几乎一无所知。假设他们不太懂好的测试设计。

**开始时宣布：** "我正在使用 writing-plans 技能来创建实施计划。"

**上下文：** 这应该在专用 worktree 中运行（由 brainstorming 技能创建）。

**计划保存到：** `docs/plans/YYYY-MM-DD-<功能名>.md`

## 小任务粒度

**每步是一个动作（2-5 分钟）：**
- "写失败的测试" - 一步
- "运行确保它失败" - 一步
- "实现让测试通过的最小代码" - 一步
- "运行测试确保通过" - 一步
- "提交" - 一步

## 计划文档头部

**每个计划必须以这个头部开始：**

```markdown
# [功能名] 实施计划

> **给 Claude：** 必需子技能：使用 superpowers-zh:executing-plans 逐任务实施此计划。

**目标：** [一句话描述这构建什么]

**架构：** [2-3 句关于方法]

**技术栈：** [关键技术/库]

---
```

## 任务结构

```markdown
### 任务 N：[组件名]

**文件：**
- 创建：`exact/path/to/file.py`
- 修改：`exact/path/to/existing.py:123-145`
- 测试：`tests/exact/path/to/test.py`

**步骤 1：写失败的测试**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**步骤 2：运行测试验证它失败**

运行：`pytest tests/path/test.py::test_name -v`
预期：FAIL "function not defined"

**步骤 3：写最小实现**

```python
def function(input):
    return expected
```

**步骤 4：运行测试验证通过**

运行：`pytest tests/path/test.py::test_name -v`
预期：PASS

**步骤 5：提交**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: 添加特定功能"
```
```

## 记住
- 总是精确的文件路径
- 计划中有完整代码（不是"添加验证"）
- 精确命令和预期输出
- 用 @ 语法引用相关技能
- DRY、YAGNI、TDD、频繁提交

## 执行交接

保存计划后，提供执行选择：

**"计划完成并保存到 `docs/plans/<文件名>.md`。两个执行选项：**

**1. 子代理驱动（本会话）** - 我为每个任务派遣新代理，任务间审查，快速迭代

**2. 并行会话（单独）** - 在新会话中打开 executing-plans，批量执行带检查点

**选哪个？"**

**如果选择子代理驱动：**
- **必需子技能：** 使用 superpowers-zh:subagent-driven-development
- 留在本会话
- 每任务新代理 + 代码审查

**如果选择并行会话：**
- 引导他们在 worktree 中打开新会话
- **必需子技能：** 新会话使用 superpowers-zh:executing-plans
