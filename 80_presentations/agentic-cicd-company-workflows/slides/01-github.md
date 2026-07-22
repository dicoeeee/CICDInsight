---
title: Slide 01 - GitHub Agentic Workflows
tags:
  - research/agentic-cicd
  - deliverable/presentation/slide
status: draft
slide: 1
content_status: content-archived
visual_status: architecture-and-copy-archived
primary_deep_dive: "[[50_deepdives/github-agentic-workflows/README]]"
as_of: 2026-07-22
---

# GitHub 以“Agentic Authoring + 确定性编译 + 受控运行”将 Agent 装配进 Actions 生命周期

> [!abstract] 页面唯一主张
> GitHub 把 Agent 放在两个不同阶段：设计时由 Authoring Agent 辅助生成可审核的 Workflow Source，运行时由 Runtime Agent 处理无法预先编码的判断；中间由不含 Agent 的确定性 Compiler 固化 Job、Step、依赖和权限，并由 Actions 与 Safe Outputs 掌握外部状态变更边界。

## 一、页面沟通任务

读完这一页，受众应理解 GitHub 如何把动态 Agent 能力装配进既有 Actions 控制结构，以及 Skill、Agent、MCP、Actions 和 Safe Output 分别承担什么职责。

本页不以某个 CI 失败、自愈或修复案例作为贯穿叙事，而直接解释 Agentic Workflow 自身的装配和运行机制。

## 二、候选观众可见文案

### 标题

GitHub 以“Agentic Authoring + 确定性编译 + 受控运行”将 Agent 装配进 Actions 生命周期

### 副标题

Authoring Agent 生成可审核声明，Compiler 固化 Job、Step 与权限，Runtime Agent 只在 Actions 控制面内动态决策。

### 当前页面架构图

![[80_presentations/agentic-cicd-company-workflows/assets/github-agentic-workflow-white.png|1400]]

> [!note] 读图口径
> Design Time 中的 Authoring Agent 与 Authoring Skill 是可选的工作流生产能力；`.md → .lock.yml` 由确定性 Compiler 完成。Run Time 的动态决策区只包含 Agent 对任务的理解、Skill 应用和 MCP/Tool 选择，Detection、Safe Outputs 与外部写入仍属于受控 Actions 机制。

### 图下内容定稿

| Design Time：可审核意图声明 | Compile Time：确定性执行计划 | Run Time：受控动态决策 |
|---|---|---|
| **解决问题：** 将业务目标、触发条件、能力需求和输出边界收敛为可版本化声明。 | **解决问题：** 将能力声明固化为标准 Actions Job Graph，而不是理解 Markdown Body 后动态拆解任务。 | **解决问题：** 在固定 Runner、DAG、权限和时限内处理无法预先枚举的判断。 |
| **关键机制：** Human / Authoring Agent + Authoring Skill；Frontmatter + Instructions；人工审查。 | **关键机制：** Parse；Schema/Expression 校验；Import 解析；Job/Permission 装配；Action SHA 与镜像固定。 | **关键机制：** Activation 注入上下文；Agent + Skill + MCP 动态执行；Detection；Safe Outputs。 |
| **阶段产物：** `workflow.md`，由人和 Agent 共同维护的 Source of Truth。 | **阶段产物：** `.lock.yml`，Actions 实际执行和审查的确定性计划。 | **阶段产物：** Candidate Artifact，以及经策略允许后创建的 PR、Issue 或 Summary。 |

> [!important] 页底核心洞察
> **设计复杂性交给 Authoring Agent，控制复杂性交给确定性 Compiler，任务不确定性交给 Runtime Agent，外部状态变更权继续由 Actions 与 Safe Outputs 掌握。**

#### 页面边界提示

- Compiler 不理解任务语义，只根据 Frontmatter、Imports 和组件配置装配预定义 Job/Step 模板；
- MCP 提供可调用能力，不代表获得对应系统的写入授权；
- Agent 生成候选变更请求，Detection 与 Safe Outputs 决定是否允许外化。

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

### 历史备选：左右分栏版式判断

> [!note] 当前版式决策
> 当前页面已采用上方完整架构图 + 下方三阶段分工的横向版式；以下左右分栏内容仅保留为演讲备注和后续局部放大页的素材，不再作为本页主版式。

左侧采用“工作流声明 → 声明转配置 → 工作流执行 → 结果输出”的四阶段结构总体合适，能够把设计时装配与运行时执行放进一条可读链路。但当前文案需要做四项调整，才能与实际机制和本页主张完全一致：

1. **“声明转配置”改为“编译与加固”。** Compiler 不是普通格式转换，而是在生成 `.lock.yml` 时完成 Schema/Expression 校验、Job 构造、权限分段、依赖拓扑和 Action SHA 固定。
2. **“Scheme 安全校验”改为“Schema / Expression 安全校验”。** `Schema` 是字段结构校验；GitHub Actions Expression Allowlist 是另一层表达式安全校验，建议在同一能力块中并列呈现。
3. **“任务解析”改为“Job 构造与权限分段”。** Markdown Body 主要在运行时加载；编译期真正被固定的是 Pre-activation、Activation、Agent、Detection、Safe Output、Conclusion 等 Job 及其权限边界。
4. **工作流执行阶段的“结果输出”改为“候选结果 Artifact”。** Agent 生成的 `agent_output.json`、Patch、Prompt、Usage 和 Firewall Log 还不是已批准结果；它们必须进入第四阶段检查和外化。

另外，“实践/定时触发”应修正为“事件/定时/命令触发”；“工具权限配置”建议拆开理解为 **Tool/MCP 可见性** 与 **Token/Job Permission 授权**，避免把工具可调用误写成已经获得权限。

### 左侧流程图建议定稿

保持四个横向阶段，每个阶段控制在五个能力块以内：

| 抽象阶段 | 建议顺序放置的能力块 | 阶段产物 |
|---|---|---|
| **1. 工作流声明** | 触发与条件定义 → 任务意图/成功标准 → Engine/Skill 选择 → Tool/MCP 与 Imports → Permission/Network/Safe Output 边界 | `workflow.md` |
| **2. 编译与加固** | Parse + Schema/Expression 校验 → Imports 解析 → Job 构造/权限分段 → 依赖校验/拓扑排序 → SHA 固定与 YAML 生成 | `.lock.yml` |
| **3. Actions 执行** | 事件/定时/命令触发 → Pre-activation 准入 → Activation 上下文与运行时装配 → Agent + Skill + Tool/MCP 执行 → 候选结果 Artifact | `agent_output.json` / Patch / Audit Log |
| **4. 输出审查与外化** | Threat Detection → Safe Output Schema/清理 → 目标/类型/数量/Protected Files 限制 → Scoped Write Job → Conclusion 与原 CI/CD Gate | PR / Issue / Comment / Dispatch / Artifact |

左侧可以再增加三类非流程块，但不应继续增加主节点：

- **阶段带：** 在第 1—2 阶段上方标“设计时装配”，第 3—4 阶段上方标“Actions 运行时”。
- **动态边界：** 主虚线框只框住 `Agent 理解/规划 → Skill 引导 → Tool/MCP 选择与调用 → 候选内容/Patch 生成`，标注“动态决策区”；Threat Detection 若使用默认 AI Engine，则另标为独立的“AI 安全判断点”。Artifact 上传、Safe Output 校验、写入和 Conclusion 保持为确定性 Actions 控制面。
- **贯穿控制条：** 在流程底部横跨四阶段放置 `Version/Hash · Permission · Network/Sandbox · Budget/Timeout/Concurrency · Audit/Telemetry`，表示这些不是单一步骤，而是跨阶段治理边界。

为了让 Skill、Agent、MCP 和 Actions 的出现位置一眼可见，可在阶段标题旁使用小型组件徽标，而不是再增加流程节点：

| 阶段 | 组件徽标建议 |
|---|---|
| 工作流声明 | Task · Skill · Tool/MCP · Control |
| 编译与加固 | Compiler · Actions |
| Actions 执行 | Actions · Agent · Skill · MCP |
| 输出审查与外化 | Artifact · Detection · Safe Output · Gate |

### Agent 参与程度的颜色规则

颜色用于表达“智能判断发生在哪里”，不用于区分 Agent 身份：

| 颜色 | 语义 | 建议色值 |
|---|---|---|
| 灰蓝 | 无 Agent，确定性系统执行 | `#DCE6F1` |
| 浅橙 | Agent 可参与或承担辅助判断 | `#FFE3A3` |
| 深橙 | Agent 主导动态决策与内容生成 | `#F59E0B` |

阶段和能力块的着色建议：

- **工作流声明：** 阶段标题用浅橙并标“Agent 可参与”；`workflow.md` 仍作为静态、可评审的阶段产物呈现。
- **编译与加固：** 所有能力块使用灰蓝，表示确定性 Compiler 行为。
- **Actions 执行：** Trigger、Pre-activation、Activation 和 Artifact 上传使用灰蓝；Agent 规划、Skill 引导、Tool/MCP 动态选择和候选结果生成使用深橙。
- **输出审查与外化：** Threat Detection 使用浅橙；Schema、清理、限制、API 写入和 Conclusion 使用灰蓝。

PR/Issue 不能整体标成“非 Agent”或“Agent 主导”，建议使用双色框表达内容与副作用的分离：

~~~text
深橙：Agent 生成标题 / 正文 / Patch / 行动建议
                 ↓
灰蓝：Safe Output 校验并调用 GitHub API 创建 PR / Issue
                 ↓
灰蓝：Conclusion 汇总实际创建对象、状态、错误和用量
~~~

如果“总结”指业务洞察、分析报告或修复建议，其内容属于深橙的 Agent 产物；如果指 Actions Run Summary / Conclusion，则属于灰蓝的系统汇总。

### 右侧技术点表格建议

右侧不要重复左侧的步骤名称，建议使用“阶段 / 技术机制 / 固化对象与控制价值”三列：

| 阶段 | 可放入 PPT 的技术点 | 固化对象与控制价值 |
|---|---|---|
| **工作流声明** | `.github/workflows/<name>.md`；YAML Frontmatter + Markdown Body；`on:` / `if:`；`engine:` / `skills:` / `imports:`；`tools:` / `mcp-servers:` / `mcp-scripts:`；`permissions:` / `network:` / `sandbox:` / `safe-outputs:`；确定性 `steps:` / `jobs:` | 把 Task、运行引擎、方法、工具面和治理边界放入同一个可版本化 Source；Tool 可见性不等于 Token 授权。 |
| **编译与加固** | Frontmatter Parsing；Schema 与 Actions Expression Allowlist；Imports 确定性 BFS 解析与字段合并；Job Construction；`needs` 循环检查与拓扑排序；Action 固定到 Commit SHA；`actions-lock.json`；Metadata/Manifest/Hash；`.lock.yml` 生成 | 将声明变成可审查的标准 Actions Job Graph，并固定依赖、权限阶段和执行拓扑；不是把 Agent 的每一步推理编译成确定性脚本。 |
| **Actions 执行** | Event/Schedule/Command；Pre-activation 的 Role、Deadline、Dedup 与 Command Gate；Activation 的上下文清理、Lock/凭据/预算检查和 Skill 安装；Agent Engine；MCP Gateway / Tool Allowlist；Sandbox / Firewall / Network Allowlist；Artifact Handoff | 动态推理只发生在只读 Agent Job 内；Actions 固定 Runner、`needs`、Timeout、Concurrency 和可观察边界。 |
| **输出审查与外化** | `agent_output.json` / `aw.patch`；默认 Threat Detection 及可插拔扫描；Safe Output JSON Schema、文本清理、目标仓库、类型与次数限制；Protected Files；`staged` 预览；独立最小写权限 Job；Conclusion Summary | Agent 决定“建议写什么”，Safe Output 决定“是否允许以及如何写入”，Conclusion 汇总“实际执行了什么”；PR/Issue 创建后仍由 Required Checks、Ruleset、Review 和 Environment 决定是否继续。 |
| **贯穿控制** | Strict Compile；Action/Container/Import/Skill Pin；Job Permission；Secret 隔离；AI Credits；Timeout/Concurrency；Artifact/Firewall Log；`gh aw logs` / `audit`；OpenTelemetry | 为 Preview 能力补上版本漂移、成本、权限、供应链和运行审计控制；这一行建议用浅色底，视觉上与四个主阶段区分。 |

如果右侧空间有限，优先保留每行前三个高辨识度技术点：

- 工作流声明：`Frontmatter + Markdown Body`、`Skill/Tool/MCP`、`Permission/Network/Safe Output`；
- 编译与加固：`Schema/Expression Validation`、`Job Graph`、`SHA Pin + .lock.yml`；
- Actions 执行：`Pre-activation/Activation`、`Agent + MCP Gateway`、`Sandbox/Artifact`；
- 输出审查：`Threat Detection`、`Safe Output Schema`、`Scoped Write + Existing Gate`。

## 四、内容块一：Workflow Source 是装配清单

`.github/workflows/<name>.md` 的真实结构只有两部分：`---` 包裹的 YAML Frontmatter，以及其后的 Markdown Body。下表按实际位置和顶层字段映射装配角色，不把概念角色伪装成同级 Schema 字段。

| `workflow.md` 实际位置或字段 | 真实声明内容 | 在装配中的角色 |
|---|---|---|
| Markdown Body | 目标、证据、判断规则、工作步骤、禁止项、输出与成功条件 | 定义 Task，即“要完成什么” |
| `engine:` | Copilot、Claude、Codex、Gemini 等 Engine 及 Model/Driver 配置 | 选择 Agent Runtime，即“由谁推理” |
| `skills:` | 在 Activation 阶段安装、固定到 Commit SHA 的外部 Skill | 注入“按什么方法工作” |
| `imports:` | 复用共享 Prompt、Tool、Step、MCP Server 与控制配置 | 组合“哪些已有配置被继承” |
| `tools:` | GitHub、Edit、Bash、Web、Playwright 等内置工具及调用限制 | 声明“可使用哪些内置能力” |
| `mcp-servers:` / `mcp-scripts:` | 外部 MCP Server 或内联 MCP Tool 的连接、认证和 Allowlist | 扩展“可调用哪些外部能力” |
| `pre-steps:` / `steps:` / `pre-agent-steps:` / `post-steps:` / `jobs:` | Agent 前后或独立 Job 中的确定性 Actions 计算 | 固定“哪些步骤不交给模型” |
| `on:` / `if:` | Event、Schedule、Command、角色和条件过滤 | 固定“何时进入作业” |
| `permissions:` / `network:` / `safe-outputs:` / Budget / Timeout | 读取权限、网络、外部写入、成本、时长和循环边界 | 固定“可以做到什么程度” |

> [!important] 字段与运行时角色不是一一同名
> `Task` 来自 Markdown Body；`Agent` 不是一个顶层 `agent:` 字段，而是 Compiler 根据 `engine:` 和任务正文生成的运行时 Job；`skills:`、`imports:`、`tools:`、`mcp-servers:` 与 `mcp-scripts:` 是相互独立的字段，而且均为按需配置。

这一内容块支撑“装配清单”的准确含义：Workflow 显式声明 Engine、可选 Skill、工具能力和控制边界，Compiler 再把这些字段装配成 Agent Job 与受控 Actions 执行计划。

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
2. `engine:` 选择 Agent Runtime，Agent 不是同名 Frontmatter 字段；
3. `skills:` 与 `imports:` 分别承担方法注入和配置复用；
4. `tools:` 与 `mcp-servers:` / `mcp-scripts:` 分别表达内置和扩展工具面；
5. `pre-steps:` / `steps:` / `pre-agent-steps:` / `post-steps:` / `jobs:` 组合确定性计算；
6. Compiler + `.lock.yml`；
7. 默认只读、Sandbox、Network 与 Tool Allowlist；
8. Candidate Artifact、Threat Detection、Safe Outputs 和最小写权限 Job；
9. 原有 CI、Ruleset、CODEOWNERS 与 Environment Approval 保持最终 Gate。

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
