---
name: requesting-code-review
name: 请求代码审查
description: Use when completing tasks, implementing major features, or before merging to verify work meets requirements
description: 完成任务、实现主要功能或合并前使用，验证工作是否符合需求
---

# Requesting Code Review
# 请求代码审查

Dispatch superpowers:code-reviewer subagent to catch issues before they cascade.
派遣 superpowers:code-reviewer Subagent，在问题扩散前捕获它们。

**Core principle:** Review early, review often.
**核心原则**：早审查，常审查。

## When to Request Review
## 何时请求审查

**Mandatory:**
**强制要求**：

- After each task in subagent-driven development
- Subagent 驱动开发中每个 Task 完成后

- After completing major feature
- 完成主要功能后

- Before merge to main
- 合并到 main 分支前

**Optional but valuable:**
**可选但有价值**：

- When stuck (fresh perspective)
- 卡住时（获得新视角）

- Before refactoring (baseline check)
- 重构前（基线检查）

- After fixing complex bug
- 修复复杂 Bug 后

## How to Request
## 如何请求

**1. Get git SHAs:**
**1. 获取 git SHA**：

```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main
                                  # 或 origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. Dispatch code-reviewer subagent:**
**2. 派遣 code-reviewer Subagent**：

Use Task tool with superpowers:code-reviewer type, fill template at `code-reviewer.md`
使用 Task 工具，类型为 superpowers:code-reviewer，填充 `code-reviewer.md` 模板

**Placeholders:**
**占位符**：

- `{WHAT_WAS_IMPLEMENTED}` - What you just built
- `{WHAT_WAS_IMPLEMENTED}` - 你刚刚构建的内容

- `{PLAN_OR_REQUIREMENTS}` - What it should do
- `{PLAN_OR_REQUIREMENTS}` - 应该实现的功能

- `{BASE_SHA}` - Starting commit
- `{BASE_SHA}` - 起始提交

- `{HEAD_SHA}` - Ending commit
- `{HEAD_SHA}` - 结束提交

- `{DESCRIPTION}` - Brief summary
- `{DESCRIPTION}` - 简要描述

**3. Act on feedback:**
**3. 根据反馈行动**：

- Fix Critical issues immediately
- **Critical** 问题立即修复

- Fix Important issues before proceeding
- **Important** 问题在继续前修复

- Note Minor issues for later
- **Minor** 问题记录待后续处理

- Push back if reviewer is wrong (with reasoning)
- 如审查员有误，据理力争

## Example
## 示例

```
[Just completed Task 2: Add verification function]
[刚完成 Task 2：添加验证函数]

You: Let me request code review before proceeding.
你：让我在继续前请求代码审查。

BASE_SHA=$(git log --oneline | grep "Task 1" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[Dispatch superpowers:code-reviewer subagent]
[派遣 superpowers:code-reviewer Subagent]

  WHAT_WAS_IMPLEMENTED: Verification and repair functions for conversation index
  WHAT_WAS_IMPLEMENTED: 对话索引的验证和修复函数

  PLAN_OR_REQUIREMENTS: Task 2 from docs/plans/deployment-plan.md
  PLAN_OR_REQUIREMENTS: docs/plans/deployment-plan.md 中的 Task 2

  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661
  DESCRIPTION: Added verifyIndex() and repairIndex() with 4 issue types
  DESCRIPTION: 添加了 verifyIndex() 和 repairIndex()，支持 4 种问题类型

[Subagent returns]:
[Subagent 返回]：

  Strengths: Clean architecture, real tests
  优点：架构清晰，测试真实

  Issues:
  问题：
    Important: Missing progress indicators
    Important：缺少进度指示器
    Minor: Magic number (100) for reporting interval
    Minor：报告间隔使用魔法数字 (100)

  Assessment: Ready to proceed
  评估：可以继续

You: [Fix progress indicators]
你：[修复进度指示器]

[Continue to Task 3]
[继续 Task 3]
```

## Integration with Workflows
## 与工作流的集成

**Subagent-Driven Development:**
**Subagent 驱动开发**：

- Review after EACH task
- 每个 Task 后审查

- Catch issues before they compound
- 在问题叠加前捕获

- Fix before moving to next task
- 修复后再进入下一个 Task

**Executing Plans:**
**执行计划**：

- Review after each batch (3 tasks)
- 每批（3 个 Task）后审查

- Get feedback, apply, continue
- 获取反馈、应用、继续

**Ad-Hoc Development:**
**临时开发**：

- Review before merge
- 合并前审查

- Review when stuck
- 卡住时审查

## Red Flags
## 危险信号

**Never:**
**绝不**：

- Skip review because "it's simple"
- 因为"很简单"就跳过审查

- Ignore Critical issues
- 忽略 Critical 问题

- Proceed with unfixed Important issues
- 带着未修复的 Important 问题继续

- Argue with valid technical feedback
- 与有效的技术反馈争论

**If reviewer wrong:**
**如审查员有误**：

- Push back with technical reasoning
- 用技术理由反驳

- Show code/tests that prove it works
- 展示代码/测试证明可行

- Request clarification
- 请求澄清

See template at: requesting-code-review/code-reviewer.md
模板参见：requesting-code-review/code-reviewer.md
