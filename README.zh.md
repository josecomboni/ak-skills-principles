# 受 Karpathy 启发的分层编码指南

> 查看我的新项目 [Multica](https://github.com/multica-ai/multica) —— 一个用于运行和管理编码智能体的开源平台，支持可复用的技能。
>
> 在 X 上关注我：[https://x.com/jiayuan_jy](https://x.com/jiayuan_jy)

面向 AI 编码智能体的分层指南，源自 [Andrej Karpathy 的观察](https://x.com/karpathy/status/2015883857489522876) 关于 LLM 编码陷阱的总结。

[English](./README.md) | 简体中文

## 问题所在

来自 Andrej 的推文：

> "模型会代你做错误假设，然后不假思索地执行。它们不管理自身的困惑，不寻求澄清，不呈现矛盾，不展示权衡，在应该提出异议时也不反驳。"

> "它们真的很喜欢把代码和 API 搞复杂，堆砌抽象概念，不清理死代码……明明 100 行能搞定的事情，非要实现成 1000 行的臃肿架构。"

> "它们有时仍会改动或删除自己理解不足的代码和注释，即使这些内容与任务本身无关。"

## 分层解决方案

关键区别：

| 层级 | 驱动方式 | 执行者 | 职责 |
|------|----------|--------|------|
| **技能** | **任务驱动** | 执行模型 | 执行一个边界清晰、契约明确的操作 |
| **编排器** | **目标驱动** | 规划/协调模型 | 将目标拆解为任务，分派技能，并验证结果 |
| **监督者** | **监督驱动** | 默认使用独立 AI/模型组；仅在明确覆盖时使用 HITL | 审查计划、diff、验证证据和可复用经验 |

技能不应该像自主规划器一样行动。编排器不应该把目标拆解塞进技能里。监督者也不应该让同一个未受约束的执行者给自己的工作打分。

## 仓库结构

| 路径 | 用途 |
|------|------|
| [`skills/karpathy-guidelines/SKILL.md`](skills/karpathy-guidelines/SKILL.md) | 任务驱动的技能指南 |
| [`orchestrators/karpathy-guidelines/ORCHESTRATOR.md`](orchestrators/karpathy-guidelines/ORCHESTRATOR.md) | 目标驱动的编排指南 |
| [`supervisors/karpathy-guidelines/SUPERVISOR.md`](supervisors/karpathy-guidelines/SUPERVISOR.md) | 监督和持续改进指南 |
| [`CLAUDE.md`](CLAUDE.md) | 分层根指令文件 |
| [`.cursor/rules/karpathy-guidelines.mdc`](.cursor/rules/karpathy-guidelines.mdc) | Cursor 项目规则 |
| [`EXAMPLES.md`](EXAMPLES.md) | 具体示例和反模式 |

## 技能层：任务驱动执行

**执行分配的任务契约。验证它。然后停止。**

任务技能使用四个原则：

| 原则 | 解决什么问题 |
|------|--------------|
| **行动前思考** | 错误假设、隐藏困惑、缺少权衡 |
| **简洁优先** | 过度复杂、臃肿抽象 |
| **精准修改** | 无关编辑、触碰不应碰的代码 |
| **任务驱动执行** | 范围漂移、输出模糊、任务完成未验证 |

作为技能行动时：

- 识别任务输入、预期输出、约束和完成条件。
- 不要把任务扩展成更大的产品目标或相邻清理工作。
- 如果任务缺少必要信息，先澄清或报告阻塞。
- 使用能证明任务契约已满足的最小验证。
- 精确报告改了什么、验证了什么、还有哪些明确阻塞，然后停止。

## 编排器层：目标驱动执行

**定义结果，拆解任务，分派执行，并验证。**

编排器承载 Karpathy 的核心洞察：

> "LLM 非常擅长循环执行直到达成特定目标……不要告诉它该做什么，给它成功标准，然后看着它完成。"

作为编排器行动时：

- 将用户目标转化为明确的成功标准和验收检查。
- 将目标拆解成边界清晰的任务，让技能可以执行而不发生范围漂移。
- 跟踪依赖并安排顺序，使每个任务都有明确契约。
- 验证任务输出和最终目标结果；不要把任务完成误认为目标完成。
- 对非琐碎工作，先写一个简短计划再分派任务。

## 监督者层：监督和持续改进

**独立审查。每次运行都学习。保持技能精简。**

监督者把 "像鹰一样盯着" 转化为可重复的监督循环：

- 优先使用与执行者/编排器不同的 AI 模型或模型组，以减少共同盲点。
- 仅在用户明确要求或用户配置的策略要求时使用 HITL。
- 审查计划、任务拆解、diff、测试结果、最终声明和范围漂移迹象。
- 将重复出现且稳定有用的修正提升为技能更新。
- 剪除过时、重复或低价值的指南，让技能保持小而锋利。
- 像代码变更一样对技能变更进行版本化和 diff 审查。

对于人类，原始的能力退化警告仍然适用：偶尔手写并审查代码，以免判断能力衰退。对于 AI 监督者，防止退化意味着自我学习，但不能让指令膨胀。

## 安装

**选项 A：Claude Code 插件（推荐）**

在 Claude Code 中，首先添加插件市场：

```text
/plugin marketplace add josecomboni/andrej-karpathy-skills
```

然后安装插件：

```text
/plugin install andrej-karpathy-skills@karpathy-skills
```

这会安装本仓库中的任务驱动技能。

**选项 B：CLAUDE.md（按项目）**

新项目：

```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/josecomboni/andrej-karpathy-skills/main/CLAUDE.md
```

已有项目（追加）：

```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/josecomboni/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md
```

## 在 Cursor 中使用

本仓库包含一个已提交的 Cursor 项目规则 ([`.cursor/rules/karpathy-guidelines.mdc`](.cursor/rules/karpathy-guidelines.mdc))，因此在 Cursor 中打开项目时同样适用这些分层指南。详情请参见 **[CURSOR.md](CURSOR.md)**，包括如何在其他项目中使用该规则，以及它与 Claude Code 的关系。

## 如何判断它在起作用

如果你看到以下情况，说明这些指南正在发挥作用：

- **技能产出边界清晰的任务结果** —— 没有顺带重构或臆造范围。
- **编排器达成已验证目标** —— 计划、任务和最终检查都能追溯到验收标准。
- **监督者在合并前发现漂移** —— 审查独立且基于证据。
- **技能改进但不膨胀** —— 重复经验被提升，重复或过时规则被剪除。

## 定制

这些指南设计用于与项目特定指令合并。将它们添加到你现有的 `CLAUDE.md` 或创建一个新的。

对于项目特定规则，添加如下章节：

```markdown
## 项目特定指南

- 使用 TypeScript 严格模式
- 所有 API 端点必须有测试
- 遵循 `src/utils/errors.ts` 中现有的错误处理模式
```

## 权衡说明

这些指南倾向于**谨慎而非速度**。对于琐碎的任务（简单的拼写错误修复、显而易见的一行修改），请自行判断 —— 并非每个改动都需要完整的严谨流程。

目标是减少非琐碎工作中的代价高昂的错误，而不是拖慢简单任务。

## 许可

MIT
