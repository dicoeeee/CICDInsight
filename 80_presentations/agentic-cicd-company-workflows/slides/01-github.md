---
title: Slide 01 - GitHub Agentic Workflows
tags:
  - research/agentic-cicd
  - deliverable/presentation/slide
status: draft
slide: 1
content_status: content-archived
visual_status: pending
primary_deep_dive: "[[50_deepdives/github-agentic-workflows/README]]"
as_of: 2026-07-21
---

# GitHub 以 Workflow 为装配清单，把 Skill、Agent 与 MCP 编译成受控的 Actions 作业

> [!abstract] 页面唯一主张
> GitHub Agentic Workflows 的关键不只是让 Coding Agent 在 Actions 中运行，而是用 Workflow Source 显式声明 Task、Engine、Skill、Tool/MCP 和控制边界，再由 Compiler 把它们生成可审查、分阶段授权的 Actions 执行计划。固定 Actions 不只是承载环境，还是包住动态推理的流程骨架和最终复验 Oracle。

## 一、页面沟通任务

读完这一页，受众应理解 GitHub 如何把动态 Agent 能力装配进既有 Actions 控制结构，以及 Skill、Agent、MCP、Actions 和 Safe Output 分别承担什么职责。

本页不以某个 CI 失败、自愈或修复案例作为贯穿叙事，而直接解释 Agentic Workflow 自身的装配和运行机制。

## 二、候选观众可见文案

### 标题

GitHub 以 Workflow 为装配清单，把 Skill、Agent 与 MCP 编译成受控的 Actions 作业

### 副标题

自然语言定义任务，Frontmatter 声明能力与边界，Compiler 生成分权限 Job，Agent 只能通过受控出口改变外部状态。

### 状态信息

- **产品：** GitHub Agentic Workflows
- **状态：** Public Preview
- **定位：** 运行在 GitHub Actions 上的 Continuous AI / Agent Workflow 层
- **典型输出：** Issue、Comment、Pull Request、Workflow Dispatch 或结构化 Artifact
- **自治口径：** 不给平台统一定级；创建可审查 PR 主要属于 L2，批准后调用受控 Workflow 可按具体动作评估为受限 L3

## 三、页面论证链

固定 Actions Event / `workflow_run`
→ `Workflow.md` 能力声明
→ Compiler 解析、校验、固定与分阶段授权
→ `.lock.yml` 受控 Actions 执行计划
→ Activation 安装 Skill、准备上下文和运行环境
→ Agent Engine 按 Skill 工作并通过 GitHub Tools / MCP 调用能力
→ Candidate Output 被缓冲为 Artifact
→ Threat Detection 与 Safe Output Job 决定哪些结果可以外化
→ 原 Actions Test / Scan / Ruleset / Environment 决定能否继续

## 四、内容块一：Workflow Source 是装配清单

`.github/workflows/<name>.md` 不是只有自然语言 Prompt，而是由机器约束的 Frontmatter 与面向 Agent 的 Markdown Body 组成。

| 声明项 | 在装配中的职责 | 页面建议用语 |
|---|---|---|
| Markdown Body / Task | 描述目标、证据、工作方法、禁止项、输出和成功条件 | 定义“要完成什么” |
| `engine:` | 选择 Copilot、Claude、Codex、Gemini 等 Agent Engine | 选择“由谁推理” |
| `skills:` / Imports | 安装运行时 Skill，或复用共享 Prompt、Tool、MCP 与配置 | 注入“按什么方法工作” |
| `tools:` / MCP / MCP Scripts | 暴露 GitHub、CLI、浏览器或外部系统的查询与行动入口 | 声明“能调用什么” |
| Steps / Jobs | 在 Agent 前后混合确定性的 Actions 计算与编排 | 固定“哪些步骤不交给模型” |
| Trigger / Permissions / Network / Budget / Safe Outputs | 限制何时运行、读取什么、访问哪里、花费多少和允许外化什么 | 固定“行动边界” |

这一内容块直接支撑“装配清单”的说法：Skill、Agent 和 MCP 都是 Workflow 的显式声明，不是运行时临时授予的广泛能力。

## 五、内容块二：Compiler 同时装配能力与控制边界

`gh aw compile` 不只是把 Markdown 翻译成 YAML，而是把能力配置和安全结构一起固化：

1. 解析 Frontmatter、Markdown Body、Imports、Skill、Tool 和 MCP 配置；
2. 校验 Schema、GitHub Actions Expression、依赖关系与部分高风险组合；
3. 固定 Action、Import 和外部依赖的版本或 Commit；
4. 生成 Pre-activation、Activation、Agent、Threat Detection、Safe Output、Custom Job 和 Conclusion 等不同权限阶段；
5. 输出 `.lock.yml` 和依赖拓扑，作为 GitHub Actions 真正执行与评审的对象。

> [!important] 本页应强调
> Compiler 的价值不是减少 YAML 编写量，而是把“Agent 能力 + 工具接口 + 权限阶段 + 输出边界”变成可版本化和可审查的执行计划。

## 六、内容块三：运行时在 Actions 中注入并组合能力

运行时应按 Compiler 生成的真实 Job 结构表达：

~~~text
GitHub Event / Schedule / Command
        ↓
Pre-activation / Activation
角色、时限、去重、版本检查、上下文准备、运行时 Skills 安装
        ↓
Agent Job
Agent Engine + Task Instructions + Skills
        ↓
GitHub Tools / MCP / CLI / Custom Tools
只在允许的 Repo、Tool、Network、Timeout 和预算内调用
        ↓
Candidate Artifacts
agent_output.json / aw.patch / Prompt / Usage / Firewall Log
~~~

### 运行时组件映射

| 组件 | 生效阶段 | 真实职责 |
|---|---|---|
| Runtime Skill | Activation 安装，Agent Job 使用 | 提供领域方法、任务步骤和组织工作方式，不授予权限 |
| Agent Engine | Agent Job | 理解上下文、规划、选择工具并生成候选行动 |
| GitHub Tools / MCP | Agent Job | 提供受限的 Repo、Issue、PR、Actions 或外部系统能力 |
| Actions Steps / Jobs | Agent 前后 | 收集确定性数据、准备环境、执行固定逻辑和留存证据 |
| Sandbox / Firewall | Agent Job | 隔离进程与网络，约束 Agent 及 MCP Server 的通信范围 |

## 七、内容块四：Agent 只提出行动，Safe Output 负责外化

Agent Job 不应直接持有通用仓库写 Token。它把“想做什么”写入结构化 JSON、Patch 或其他 Artifact，随后进入独立的输出控制阶段：

~~~text
Candidate Artifact
        ↓
Threat Detection
检查 Secret Leak、Prompt Injection、恶意 Patch 和策略违规
        ↓
Safe Output Handler
Schema 校验、文本清理、目标仓库、次数和类型限制
        ↓
Scoped Write Job
以最小写权限创建 Issue / Comment / PR 或调用受控 Workflow
        ↓
原有 Ruleset / CI / CODEOWNERS / Environment Approval
决定是否合并、发布或继续执行
~~~

这一块证明“受控的 Actions 作业”：真正的外部副作用由受信 Job 外化，而不是由主 Agent 直接执行。

### 与固定 GitHub Actions CI/CD 的三层关系

| 关系层 | 固定 Actions 提供什么 | Agentic Workflow 增加什么 |
|---|---|---|
| 编译层 | Event、Runner、Job、`needs`、Permission、Artifact 和执行拓扑 | 把 Task、Agent、Skill、MCP 与输出边界编译成传统 `.lock.yml` |
| 运行层 | 确定性 Step/Job 在 Agent 前后收集事实、过滤触发和执行固定逻辑 | 在受限 Agent Job 内处理难以预先枚举的归纳、假设和 Tool 选择 |
| 反馈层 | 原 CI、Ruleset、Environment 与人工批准复验并放行 | 通过 Artifact、PR 或受控 Dispatch 提交候选结果 |

> [!important] 页面关系句
> **流程拓扑固定，局部判断动态，最终放行确定。** GitHub 不是用 Agent 替换 Actions，而是在固定 Actions 骨架中加入一个有输入、预算、工具和出口边界的动态决策段。

页面讲解需补充一个安全边界：自定义 `steps:` / `jobs:` 仍按标准 Actions 安全模型运行，并不自动继承 Agent Firewall；需要分别审查 Action、Shell、Secret、Permission 与 Runner。

## 八、组件角色结论

| 组件 | 一句话角色 | 不能误解为 |
|---|---|---|
| Skill | 定义“怎么做” | 权限、Policy 或执行授权 |
| Agent | 决定“下一步做什么” | 成功标准或最终批准者 |
| MCP / Tool | 提供“能调用什么” | 自动获得外部系统写权限 |
| Actions | 固定流程拓扑，控制何时、何地、以什么权限运行，并复验结果 | 被自然语言流程取代的旧系统 |
| Safe Output | 决定“哪些候选结果可以改变外部状态” | 对 Agent 输出无条件放行 |
| Oracle / Gate | 证明“结果是否正确、是否可以继续” | Agent 的自我评价 |

建议把以下结论放在页面底部：

> Skill 定义方法，Agent 动态决策，MCP 扩展能力面；Actions 固定流程骨架并复验结果，Safe Output 掌握外部写入边界。

## 九、必须明确区分的相邻概念

### 设计时 Skill/MCP 与运行时 Skill/MCP

| 类型 | 使用者与阶段 | 用途 | 是否进入本页主流程 |
|---|---|---|---|
| `agentic-workflows` Authoring Skill | 外部 Coding Agent 在设计、更新和调试时使用 | 生成 Workflow Source、配置 Tool/Permission 并编译 | 只做侧注，不与运行时 Skill 合并 |
| `gh-aw` Authoring MCP | 外部 Coding Agent 管理 Workflow 生命周期时使用 | Compile、Audit、Logs、Status 等管理能力 | 只做侧注，不画进 Agent Job |
| Frontmatter `skills:` | Activation 安装，运行中的 Agent 使用 | 注入运行时方法和领域知识 | 进入主流程 |
| Runtime MCP / GitHub Tools | Agent Job 初始化并调用 | 查询 Repo、Issue、PR、Actions 或外部系统 | 进入主流程 |

### Agentic Workflows 与相邻 GitHub 能力

| 能力 | 页面边界 |
|---|---|
| GitHub Agentic Workflows | 本页主体：事件驱动、编译到 Actions、运行受约束 Coding Agent |
| Copilot Coding Agent | 面向一次性委派开发任务并交付 PR，不等同通用 Workflow 编排 |
| Copilot Code Review | 专注 PR Review，不等同 Agentic Workflow |
| GitHub MCP Server | 提供 GitHub Tool Protocol 接口，不是 Agent、Scheduler、Compiler 或 Actions Workflow |

## 十、关键功能点取舍

### 必须进入页面

1. Markdown Body + Frontmatter 组成 Workflow Source；
2. 多 Agent Engine 可替换；
3. Runtime Skills 与 Imports；
4. GitHub Tools、MCP 和确定性 Steps/Jobs 的组合；
5. Compiler + `.lock.yml`；
6. 默认只读、Sandbox、Network 与 Tool Allowlist；
7. Candidate Artifact、Threat Detection、Safe Outputs 和最小写权限 Job；
8. 原有 CI、Ruleset、CODEOWNERS 与 Environment Approval 保持最终 Gate。

### 只作为页脚状态或讲解补充

- Public Preview、版本升级和兼容性运营；
- AI Credits、Actions Minutes 和 OpenTelemetry；
- 多仓 Orchestrator/Worker、Side Repo、CentralRepoOps；
- 具体客户引语和厂商效果数据；
- 完整八阶段覆盖。

## 十一、页底三条洞察

- **核心差异化：** GitHub 用 Compiler 把 Agent、Skill、MCP 和权限边界一起落到既有 Actions 执行图；动态推理成为固定流程中的受控一段，而不是另建平行 AI Pipeline。
- **企业启示：** Agent 平台的关键不是 Tool 数量，而是能力能否被声明、固定、分阶段授权并由外部 Gate 复验。
- **当前边界：** Agentic Workflows 仍处于 Public Preview；关键合并、制品晋级和生产发布不能仅凭 Agent 与 Safe Output 自动放行。

## 十二、明确排除

- 不以 CI 失败自愈或任何单一业务案例作为贯穿作业流；
- 不引用、复用或重建已删除的旧 HTML；
- 不在本页展开多仓编排、成本指标、完整产品矩阵或八阶段覆盖；
- 不把 GitHub MCP Server GA 写成 Agentic Workflows GA；
- 不把 Public Preview、Safe Output 或受限 Tool Call 外推为生产级全自治；
- 不让标题中的“Skill、Agent、MCP”退化为脱离运行阶段的功能图标。

## 十三、状态与证据口径

- Agentic Workflows：2026-06-11 进入 Public Preview；
- Copilot、Claude、Codex、Gemini 是当前正式列出的主要 Agent Engine，具体能力并不完全对齐；
- Runtime Skill 在 Activation 阶段安装，外部 Skill 应固定到具体 Commit；
- 主 Agent 默认只读，写操作通过声明的 Safe Outputs 和独立 Job 外化；
- Threat Detection 是额外防线，不等于确定性安全 Oracle；
- 产品状态、任务自治、生产证据和业务价值必须分开表达。

## 十四、来源

### 工作区分析

- [[20_summaries/companies/README#1. GitHub：把 Agent 编译进 Actions|GitHub 公司总结]]
- [[50_deepdives/github-agentic-workflows/90_report|GitHub Agentic Workflows 深研]]
- [[50_deepdives/github-agentic-workflows/fixed-actions-relationship-research|Agentic Workflow 与固定 Actions 关系证据]]
- [[50_deepdives/github-agentic-workflows/20_evidence-map|GitHub Agentic Workflows 证据矩阵]]
- [[50_deepdives/github-agentic-workflows/60_playbook|GitHub Agentic Workflows 企业 Playbook]]
- [[60_tutorials/github-agentic-workflows-config|GitHub Agentic Workflows 配置速查]]
- [[80_presentations/agentic-cicd-company-workflows/source-map#Source Map|本页 Source Map]]

### 一手入口

- [[00_sources/briefs/2026-github-agentic-workflows|GitHub Agentic Workflows 产品文档 Brief]]
- [[00_sources/briefs/2026-github-gh-aw-open-source|github/gh-aw 开源实现 Brief]]
- [[00_sources/briefs/2025-github-remote-mcp-server-ga|GitHub Remote MCP Server Brief]]
- [About Workflows](https://github.github.com/gh-aw/introduction/overview/)
- [Frontmatter Reference](https://github.github.com/gh-aw/reference/frontmatter/)
- [Security Architecture](https://github.github.com/gh-aw/introduction/architecture/)
- [Compilation Process](https://github.github.com/gh-aw/reference/compilation-process/)
- [DeterministicOps](https://github.github.com/gh-aw/patterns/deterministic-ops/)
- [Custom Steps and Jobs](https://github.github.com/gh-aw/reference/steps-jobs/)
- [Triggers](https://github.github.com/gh-aw/reference/triggers/)
- [Triggering CI](https://github.github.com/gh-aw/reference/triggering-ci/)
- [Safe Outputs](https://github.github.com/gh-aw/reference/safe-outputs/)
- [Public Preview Announcement](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview/)
