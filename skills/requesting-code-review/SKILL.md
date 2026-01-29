---
name: requesting-code-review
description: 当完成任务、实施主要功能或合并前需要验证工作满足需求时使用
---

# 请求代码审查

派遣 superpowers:code-reviewer 子代理在问题级联前抓住它们。

**核心原则：** 早审查，常审查。

## 何时请求审查

**强制：**
- 子代理驱动开发中每个任务后
- 完成主要功能后
- 合并到 main 前

**可选但有价值：**
- 卡住时（新视角）
- 重构前（基线检查）
- 修复复杂 bug 后

## 如何请求

**1. 获取 git SHA：**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # 或 origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. 派遣 code-reviewer 子代理：**

使用 Task 工具，类型为 superpowers:code-reviewer，填充 `code-reviewer.md` 的模板

**占位符：**
- `{WHAT_WAS_IMPLEMENTED}` - 你刚构建了什么
- `{PLAN_OR_REQUIREMENTS}` - 它应该做什么
- `{BASE_SHA}` - 起始提交
- `{HEAD_SHA}` - 结束提交
- `{DESCRIPTION}` - 简要摘要

**3. 根据反馈行动：**
- 立即修复严重（Critical）问题
- 继续前修复重要（Important）问题
- 记录次要（Minor）问题稍后处理
- 如果审查员错了就反驳（附理由）

## 示例

```
[刚完成任务 2：添加验证函数]

你：在继续前让我请求代码审查。

BASE_SHA=$(git log --oneline | grep "Task 1" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[派遣 superpowers:code-reviewer 子代理]
  WHAT_WAS_IMPLEMENTED: 对话索引的验证和修复函数
  PLAN_OR_REQUIREMENTS: docs/plans/deployment-plan.md 的任务 2
  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661
  DESCRIPTION: 添加了 verifyIndex() 和 repairIndex() 带 4 种问题类型

[子代理返回]：
  优点：干净架构，真实测试
  问题：
    重要：缺少进度指示器
    次要：魔术数字（100）用于报告间隔
  评估：准备好继续

你：[修复进度指示器]
[继续任务 3]
```

## 与工作流集成

**子代理驱动开发：**
- 每个任务后审查
- 在问题复合前抓住
- 转到下个任务前修复

**执行计划：**
- 每批（3 个任务）后审查
- 获取反馈，应用，继续

**临时开发：**
- 合并前审查
- 卡住时审查

## 红旗

**永远不要：**
- 因为"简单"跳过审查
- 忽略严重问题
- 有未修复重要问题时继续
- 与有效技术反馈争论

**如果审查员错了：**
- 用技术理由反驳
- 展示证明它有效的代码/测试
- 请求澄清

模板见：requesting-code-review/code-reviewer.md
