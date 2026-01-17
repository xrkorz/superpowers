# Superpowers 插件文件完整解析

> 本文档逐一解析 `_source/superpowers/` 目录下每个文件的作用

---

## 📂 根目录文件

| 文件 | 作用 |
|------|------|
| `README.md` | 插件主文档，包含安装指南、基本工作流、技能库概述 |
| `RELEASE-NOTES.md` | 版本发布日志 |
| `LICENSE` | MIT 许可证 |
| `.gitignore` | Git 忽略规则 |

---

## 📂 commands/ — 用户命令入口

| 文件 | 作用 | 触发的 Skill |
|------|------|-------------|
| `brainstorm.md` | `/superpowers:brainstorm` 命令定义 | → `brainstorming` |
| `write-plan.md` | `/superpowers:write-plan` 命令定义 | → `writing-plans` |
| `execute-plan.md` | `/superpowers:execute-plan` 命令定义 | → `executing-plans` |

**格式说明**：
```yaml
---
description: "命令描述"
disable-model-invocation: true  # 命令仅触发 skill，不直接调用模型
---
Invoke the superpowers:<skill-name> skill and follow it exactly
```

---

## 📂 hooks/ — 生命周期钩子

| 文件 | 作用 |
|------|------|
| `hooks.json` | 钩子配置，定义 `SessionStart` 事件触发 `session-start.sh` |
| `run-hook.cmd` | **跨平台 Polyglot 包装器**，在 Windows 上调用 Git Bash 执行 `.sh` 脚本 |
| `session-start.sh` | 会话启动时注入 `using-superpowers` 技能内容到 Claude 上下文中 |

**`run-hook.cmd` 工作原理**：
```batch
# Windows 部分：调用 Git Bash 执行 .sh 脚本
"C:\Program Files\Git\bin\bash.exe" -l "%~dp0%~1" %2 %3...

# Unix 部分（被 Bash 执行）
"${SCRIPT_DIR}/${SCRIPT_NAME}" "$@"
```

**`session-start.sh` 核心逻辑**：
1. 读取 `skills/using-superpowers/SKILL.md` 内容
2. 包装为 `<EXTREMELY_IMPORTANT>` 标签
3. 输出 JSON 格式注入到 Claude 会话上下文

---

## 📂 agents/ — Agent 角色定义

| 文件 | 作用 |
|------|------|
| `code-reviewer.md` | 代码审查 Agent 定义，包含审查流程、分类标准、输出格式 |

**Agent 格式**：
```yaml
---
name: code-reviewer
description: 触发条件描述
model: inherit  # 继承父级模型
---
# Agent 系统提示词
```

---

## 📂 lib/ — 核心库

| 文件 | 作用 |
|------|------|
| `skills-core.js` | 技能系统核心库（Node.js） |

**导出函数**：
| 函数 | 作用 |
|------|------|
| `extractFrontmatter(filePath)` | 从 SKILL.md 解析 YAML frontmatter |
| `findSkillsInDir(dir, sourceType, maxDepth)` | 递归查找目录中所有 SKILL.md |
| `resolveSkillPath(skillName, superpowersDir, personalDir)` | 解析技能路径，支持 `superpowers:` 前缀和个人技能优先 |
| `checkForUpdates(repoDir)` | 检查 Git 仓库是否有可用更新 |
| `stripFrontmatter(content)` | 移除 YAML frontmatter，返回纯内容 |

---

## 📂 skills/ — 技能库

### 设计阶段

| 技能目录 | SKILL.md 作用 | 辅助文件 |
|---------|--------------|---------|
| `brainstorming/` | 头脑风暴技能：通过提问探索需求，逐步呈现设计 | — |
| `writing-plans/` | 计划制定技能：生成 2-5 分钟粒度的微任务计划 | — |

### 实现阶段

| 技能目录 | SKILL.md 作用 | 辅助文件 |
|---------|--------------|---------|
| `using-git-worktrees/` | Git Worktree 隔离工作区技能 | — |
| `subagent-driven-development/` | 子 Agent 驱动开发：每个 Task 派遣独立 Agent | `implementer-prompt.md` (实现者提示词)<br>`spec-reviewer-prompt.md` (规格审查提示词)<br>`code-quality-reviewer-prompt.md` (代码质量审查提示词) |
| `executing-plans/` | 批量执行计划技能：分批执行 + 人工检查点 | — |
| `dispatching-parallel-agents/` | 并行 Agent 调度：处理独立问题时并发派遣 | — |

### 质量保障

| 技能目录 | SKILL.md 作用 | 辅助文件 |
|---------|--------------|---------|
| `test-driven-development/` | TDD 技能：红-绿-重构循环 | `testing-anti-patterns.md` (测试反模式) |
| `systematic-debugging/` | 系统化调试：4 阶段根因分析 | `root-cause-tracing.md` (根因追踪)<br>`defense-in-depth.md` (纵深防御)<br>`condition-based-waiting.md` (条件等待)<br>`condition-based-waiting-example.ts` (示例代码)<br>`find-polluter.sh` (污染器查找脚本)<br>`test-*.md` (测试场景) |
| `requesting-code-review/` | 请求代码审查技能 | `code-reviewer.md` (审查模板，带占位符) |
| `receiving-code-review/` | 接收代码审查技能：技术评估而非表演性认同 | — |
| `verification-before-completion/` | 完成前验证：禁止无证据的完成声明 | — |

### 收尾阶段

| 技能目录 | SKILL.md 作用 | 辅助文件 |
|---------|--------------|---------|
| `finishing-a-development-branch/` | 分支收尾：测试验证 → 选项呈现 → 执行选择 → 清理 | — |

### 元技能

| 技能目录 | SKILL.md 作用 | 辅助文件 |
|---------|--------------|---------|
| `using-superpowers/` | 技能系统使用指南：强制在任何响应前检查技能 | — |
| `writing-skills/` | 编写技能指南：TDD 应用于文档 | `anthropic-best-practices.md` (Anthropic 官方最佳实践)<br>`graphviz-conventions.dot` (Graphviz 风格规范)<br>`persuasion-principles.md` (说服原则研究)<br>`render-graphs.js` (DOT 渲染脚本)<br>`testing-skills-with-subagents.md` (子 Agent 测试方法)<br>`examples/` (示例目录) |

---

## 📂 docs/ — 文档

| 文件/目录 | 作用 |
|----------|------|
| `README.codex.md` | Codex 平台安装和使用指南 |
| `README.opencode.md` | OpenCode 平台安装和使用指南 |
| `testing.md` | 测试方法论文档 |
| `plans/` | 设计/计划文档存放目录 |
| `windows/` | Windows 特定文档 |
| `superpowers_workflow.dot` | Superpowers 工作流 DOT 图 |
| `superpowers_workflow.png` | 工作流图渲染结果 |

---

## 📂 .claude-plugin/ — Claude Code 插件配置

| 文件 | 作用 |
|------|------|
| `plugin.json` | 插件元数据（名称、版本、描述） |
| `marketplace.json` | 插件市场配置 |

---

## 📂 .codex/ — Codex 平台配置

| 文件 | 作用 |
|------|------|
| `INSTALL.md` | Codex 安装指南（被 Agent 获取执行） |
| `superpowers-bootstrap.md` | Codex 引导脚本 |
| `superpowers-codex` | Codex 专用入口脚本 |

---

## 📂 .opencode/ — OpenCode 平台配置

| 文件 | 作用 |
|------|------|
| `INSTALL.md` | OpenCode 安装指南 |
| `plugin/superpowers.js` | OpenCode 插件入口 |

---

## 📂 tests/ — 测试文件

包含 42 个测试文件，用于验证技能和核心库的正确性。

---

## 文件关系概览

```
用户输入
    ↓
commands/*.md  ──触发──→  skills/*/SKILL.md
    │                           │
    │                           ├── 调用辅助文件 (*.md, *.ts, *.sh)
    │                           │
    │                           ├── 调用 agents/*.md
    │                           │
    │                           └── 相互引用其他 skills
    │
hooks/hooks.json
    │
    └── SessionStart ──→ run-hook.cmd ──→ session-start.sh
                                              │
                                              └── 注入 using-superpowers 到上下文
```

---

## 辅助文件详细解析

### 📂 subagent-driven-development/ 提示词模板

| 文件 | 作用 |
|------|------|
| `implementer-prompt.md` | 实现者 Agent 的提示词模板，包含任务结构、上下文、自查清单和报告格式 |
| `spec-reviewer-prompt.md` | 规格审查者 Agent 的提示词模板，**不信任实现者报告**，强制独立验证代码 |
| `code-quality-reviewer-prompt.md` | 代码质量审查者提示词，指向 `requesting-code-review/code-reviewer.md` |

### 📂 systematic-debugging/ 调试子技能

| 文件 | 作用 |
|------|------|
| `root-cause-tracing.md` | 根因追踪指南：向上追溯调用链直到找到原始触发点 |
| `defense-in-depth.md` | 纵深防御指南：在数据流经的每一层都添加验证 |
| `condition-based-waiting.md` | 条件等待指南：用实际条件替代任意超时 |
| `condition-based-waiting-example.ts` | 条件等待 TypeScript 示例代码（可直接适配） |
| `find-polluter.sh` | 测试污染者二分查找脚本 |
| `test-*.md` | 压力测试场景（用于验证技能的 TDD 测试） |
| `CREATION-LOG.md` | 技能创建日志（记录 TDD 迭代过程） |

### 📂 test-driven-development/ TDD 子技能

| 文件 | 作用 |
|------|------|
| `testing-anti-patterns.md` | 测试反模式参考：禁止测试 Mock 行为、禁止生产代码添加测试专用方法等 |

### 📂 requesting-code-review/ 审查模板

| 文件 | 作用 |
|------|------|
| `code-reviewer.md` | 代码审查 Agent 提示词模板，带占位符 `{WHAT_WAS_IMPLEMENTED}` 等 |

### 📂 writing-skills/ 技能编写子资源

| 文件 | 作用 |
|------|------|
| `anthropic-best-practices.md` | Anthropic 官方 Agent Skills 最佳实践（1151 行） |
| `graphviz-conventions.dot` | Graphviz DOT 文件风格规范 |
| `persuasion-principles.md` | 说服原则研究（用于防止 Agent 合理化违规） |
| `render-graphs.js` | DOT 图渲染脚本（生成 SVG） |
| `testing-skills-with-subagents.md` | 子 Agent 测试方法论 |
| `examples/CLAUDE_MD_TESTING.md` | CLAUDE.md 测试示例 |

---

## 平台入口文件详解

### Claude Code 入口 (.claude-plugin/)

| 文件 | 作用 |
|------|------|
| `plugin.json` | 插件元数据：名称 `superpowers`、版本 `4.0.3`、作者、仓库地址 |
| `marketplace.json` | 开发市场配置：`superpowers-dev` 市场的插件列表 |

### Codex 入口 (.codex/)

| 文件 | 作用 |
|------|------|
| `INSTALL.md` | Codex 安装指南（克隆仓库 + 配置 AGENTS.md） |
| `superpowers-bootstrap.md` | Codex 引导内容（工具映射 + 规则声明） |
| `superpowers-codex` | Codex CLI 入口脚本（shell） |

### OpenCode 入口 (.opencode/)

| 文件 | 作用 |
|------|------|
| `INSTALL.md` | OpenCode 安装指南（克隆 + 符号链接 + 技能优先级） |
| `plugin/superpowers.js` | OpenCode 插件（216 行）：导出 `use_skill` 和 `find_skills` 工具，处理会话事件注入 |

---

## 验证：已查看的文件总数

共 **97 个非 .git 文件**，全部已查看或通过目录结构推断：

| 类别 | 数量 | 状态 |
|------|------|------|
| Skills SKILL.md | 13 | ✅ 已查看 |
| Skills 辅助文件 | ~25 | ✅ 已查看 |
| Commands | 3 | ✅ 已查看 |
| Hooks | 3 | ✅ 已查看 |
| Agents | 1 | ✅ 已查看 |
| Lib | 1 | ✅ 已查看 |
| Docs | ~8 | ✅ 已查看 |
| 平台配置 (.claude-plugin/.codex/.opencode) | 8 | ✅ 已查看 |
| tests/ | ~42 | ✅ 推断（测试脚本）|
| 根目录 | 4 | ✅ 已查看 |
