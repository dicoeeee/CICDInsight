---
title: Harness Intelligent Workflow Orchestration 核验与洞察
aliases:
  - Harness 智能工作流编排
  - Harness AI Workflow Orchestration 2026-07-24
tags:
  - research/agentic-cicd
  - research/deep-dive
  - company/harness
  - workflow-orchestration
status: complete
as_of: 2026-07-24
topic_id: harness-company
topic_type: capability
scope: intelligent-workflow-orchestration
source_policy: official-primary-only
confidence: medium-high
presentation_ready: true
---

# Harness Intelligent Workflow Orchestration 核验与洞察

> [!abstract] 核心结论
> Harness 所说的 **Intelligent Workflow Orchestration** 不是一个有独立 SKU、API 或 Runtime 的新产品，而是 Harness AI 的平台级能力支柱：用 Agent 理解意图、生成或优化流程，并由跨模块的 Pipeline 与模块工作流把 Agent、确定性 Step、测试、审批、回滚和外部动作连接成端到端作业。
>
> 最准确的架构判断不是“Agent 自动取代 Pipeline”，而是 **Agent 让流程具备推理与行动能力，Pipeline 继续提供可见、可复用、可审计的确定性控制结构**。Harness 已支持通过 API 动态提交 Pipeline YAML，以及在运行中追加 Dynamic Stage；但这是一项需要 Feature Flag 的高级能力，并不等于平台内置了一个会自主重构整个跨模块 DAG 的通用 Agent 编排器。

## 一、官方定义与产品边界

Harness AI 产品页把 Intelligent Workflow Orchestration 定义为：

> “A workflow layer that sits across all Harness modules, orchestrating actions like pipeline creation, troubleshooting, test runs, rollbacks, and approvals end-to-end.”

即：横跨 Harness 各模块的工作流层，端到端编排 Pipeline 创建、故障排查、测试执行、回滚和审批等动作。[Harness AI 产品页](https://www.harness.io/products/harness-ai)

这段定义给出了三个边界：

1. **它是横跨模块的工作流层，而不是单个领域 Agent。** Harness AI 的另外两个支柱分别是 Agentic Flows 和 Software Delivery Knowledge Graph；Intelligent Workflow Orchestration 负责把智能体和上下文转化为可以连续执行、受治理的作业。[Harness AI 产品页](https://www.harness.io/products/harness-ai)
2. **它覆盖流程创建和流程执行两端。** DevOps Agent 能创建、修改和总结多模块 Pipeline；Pipeline 引擎再运行测试、Agent、审批、部署和回滚等节点。[DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/)、[Harness AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/)
3. **它不是官方单独列出的产品对象。** 当前 Harness AI 功能状态表逐项列出 DevOps Agent、Harness Agents、MCP、AI Test、Release Agent 等能力，但没有一项独立名为 Intelligent Workflow Orchestration。因此不能给这一营销支柱整体标注一个统一的 GA/Beta 状态，应按组成能力分别核验。[Harness AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/)

因此，中文名称建议使用：

> **跨模块智能工作流编排**

“智能”来自 Agent 与上下文，“编排”主要由 Pipeline 和模块工作流承载，“跨模块”是它区别于单点 Agent 功能的价值所在。

它也不应与独立的 **Harness Release Orchestration** 产品混淆：后者面向企业发布管理，以 Release Process、Phase、Activity、Release Group 和 Calendar 为核心对象；Intelligent Workflow Orchestration 则是 Harness AI 对跨模块智能化与工作流能力的横向概括。[Release Orchestration](https://www.harness.io/blog/introducing-harness-release-orchestration)

## 二、它到底在编排什么

Intelligent Workflow Orchestration 是 **Pipeline-centered，但不是 Pipeline-only**。

| 编排对象 | 实际机制 | 是否属于核心编排对象 | 证据与边界 |
|---|---|---:|---|
| Pipeline 定义 | DevOps Agent 根据自然语言创建、修改 CI、CD、IaCM、IDP、SCS、STO、DB DevOps 和 Chaos 等多模块 Pipeline | 是 | [DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/)、[Harness AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/) |
| Stage / Step 执行图 | Pipeline 定义顺序、并行、条件、循环、矩阵、依赖、超时和失败处理 | 是 | [CD Overview](https://developer.harness.io/docs/continuous-delivery/overview/)、[DAG Pipelines](https://developer.harness.io/docs/platform/pipelines/dag-pipelines/) |
| 动态 Pipeline / Dynamic Stage | 外部系统通过 API 提交运行时 YAML，或前序 Step 生成 YAML 后由 Dynamic Stage 追加并执行 Stage/Step | 是，但为高级动态入口 | [Dynamic Pipeline Execution](https://developer.harness.io/docs/platform/pipelines/dynamic-execution-pipeline/)、[Dynamic Stage](https://developer.harness.io/docs/platform/pipelines/add-a-stage/) |
| Worker Agent | Agent Definition 被 Pipeline 中的 Agent Step 引用，可与脚本、插件、审批及其他 Agent 组合 | 是，作为一种 Step | [Worker Agents 发布说明](https://www.harness.io/blog/introducing-autonomous-worker-agents)、[Worker Agent Configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/) |
| 领域模块动作 | AI Test、FME、GitOps、STO 等能力可以作为模块专用 Step 或动作进入同一 Pipeline | 是 | [AI Test in CI/CD](https://developer.harness.io/docs/ai-test-automation/integrations/harness-cd/)、[FME with Pipelines](https://developer.harness.io/docs/feature-management-experimentation/pipelines/) |
| MCP Tool / Connector | Agent 在自己的推理循环中调用 Harness 或第三方 Tool；Connector 提供连接和凭据 | 间接，不等于 Pipeline 节点 | [Harness MCP Server](https://developer.harness.io/docs/platform/harness-ai/harness-mcp-server/)、[Worker Agent Configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/) |
| Knowledge Graph / HQL | 为 Agent 的查询、分析和决策提供结构化上下文 | 否，是上下文层 | [Knowledge Graph + HQL](https://www.harness.io/blog/why-harness-ai-uses-knowledge-graph) |
| Pipeline Context | 通过表达式、输入、触发器信息、前序输出和共享 Workspace 把本次执行状态传给 Agent/Step | 否，是运行上下文 | [Worker Agent Configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/)、[Worker Agents 发布说明](https://www.harness.io/blog/introducing-autonomous-worker-agents) |
| IDP Workflow | 前端表单接收输入，后端 Action 和 Harness Pipeline 执行自助作业 | 是，Pipeline 之外的入口/封装 | [Self Service Workflows](https://developer.harness.io/3k-docs/internal-developer-portal/flows/overview/)、[Workflow Patterns](https://developer.harness.io/docs/internal-developer-portal/adoption/why-workflows/) |
| AI SRE Runbook | 由 Incident/Event 触发预定义动作序列，可调用 Harness Pipeline 或外部系统 | 是，模块原生工作流 | [AI SRE Runbooks](https://developer.harness.io/docs/ai-sre/runbooks/)、[Create Runbooks](https://developer.harness.io/docs/ai-sre/runbooks/create-runbook/) |

### 关键判断

- **Pipeline 是主要编排骨架。** Harness 官方把 Pipeline 定义为跨模块、由 Stage、Step 和 Trigger 构成的端到端自动化工作流；Worker Agent、AI Test 和 FME 操作都可以进入这条骨架。[Harness Platform](https://developer.harness.io/docs/platform/)、[AI Test in CI/CD](https://developer.harness.io/docs/ai-test-automation/integrations/harness-cd/)、[FME with Pipelines](https://developer.harness.io/docs/feature-management-experimentation/pipelines/)
- **Agent Step 不是编排器本身。** Agent Step 在局部任务中多轮推理并调用工具；多个 Agent 的顺序、并行、条件和矩阵关系仍由 Pipeline 定义，输出通过 Pipeline Expression 或共享 Workspace 传递。[Worker Agents 发布说明](https://www.harness.io/blog/introducing-autonomous-worker-agents)
- **Pipeline 可以动态生成，但生成者与执行器仍然分离。** Dynamic Execution API 接收外部系统生成的完整 Pipeline YAML；Dynamic Stage 可以消费前序 Step 生成的 YAML 并在运行中追加 Stage/Step。Harness 官方同时提醒，这类自改写流程复杂度高，通常应保留稳定骨架，只把测试、验证或部署等可变部分动态化。[Dynamic Pipeline Execution](https://developer.harness.io/docs/platform/pipelines/dynamic-execution-pipeline/)、[Dynamic Pipelines 官方说明](https://www.harness.io/blog/harness-dynamic-pipelines-complete-adaptability-rock-solid-governance)
- **Tool 与 Context 不应画成 Pipeline 的同级“流程节点”。** Knowledge Graph/HQL 回答“平台已知什么”，Pipeline Context 回答“本次发生什么”，MCP/Connector 提供“可以调用什么”；Pipeline 才决定这些能力在何时进入作业以及其结果如何影响后续节点。
- **跨模块不等于所有模块共享同一 Runtime。** AI Test、FME 和 AI SRE 都有自己的领域对象和执行方式；Harness 通过 Pipeline Step、API/Connector 或 Runbook 把它们串起来，而不是把它们改造成同一种 Worker Agent Runtime。[AI Test in CI/CD](https://developer.harness.io/docs/ai-test-automation/integrations/harness-cd/)、[FME with Pipelines](https://developer.harness.io/docs/feature-management-experimentation/pipelines/)、[AI SRE Runbooks](https://developer.harness.io/docs/ai-sre/runbooks/)

## 三、智能化与确定性控制如何分工

Harness 的官方战略表述很直接：它投资于 Knowledge Graph、Focused AI Agents，以及一个让 AI 决策保持一致和安全的可靠编排引擎。[Harness 公司战略说明](https://www.harness.io/blog/240m-financing-to-bring-ai-to-everything-after-code) Harness 对 Pipeline 的进一步表述是：Pipeline 不是 Agent，而是 Agent 行动在 Policy 下被评估、约束和执行的控制面；Agent 只是 Pipeline 能运行的一类 Step。[AI writes the code. Who delivers it safely?](https://www.harness.io/blog/ai-writes-the-code-who-delivers-it-safely)

| 智能化负责 | 确定性控制负责 |
|---|---|
| 理解自然语言意图 | 保存和版本化 Pipeline / Agent Definition |
| 生成、修改或优化 Pipeline | Stage / Step 的顺序、并行、依赖与循环 |
| 基于 Knowledge Graph/HQL 和 Pipeline Context 分析问题 | 运行时输入、条件表达式和输出传递 |
| 在 Agent Step 内选择 Tool、形成计划并执行局部多轮动作 | 超时、最大轮次、重试、失败策略和回滚 |
| 由外部 Agent/程序按本次任务生成 Pipeline 或 Stage YAML | Dynamic Execution/Dynamic Stage 对 YAML 做校验、解析、执行与记录 |
| 分析失败、提出修复或生成变更 | Approval、RBAC、OPA 与审计证据 |

这形成了一种“双向嵌入”关系：

1. **Agent 编排 Pipeline**：DevOps Agent 可以生成或修改 Pipeline 的 Stage、Step、策略和 GitOps 操作；在 GitOps 场景中，Harness 还明确展示了 Agent 根据失败模式添加验证门禁和调整超时的用法。[DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/)、[AI for GitOps](https://www.harness.io/blog/ai-for-gitops-tame-your-argo-sprawl)
2. **Pipeline 编排 Agent**：Worker Agent 作为 Pipeline Step 与脚本、插件、审批和其他 Agent 组合；Pipeline Engine 管理容器、凭据、MCP 集成、审计和成本。[Worker Agents 发布说明](https://www.harness.io/blog/introducing-autonomous-worker-agents)
3. **外部智能按需生成 Pipeline**：Dynamic Execution API 允许外部 Agent、IDP 或程序传入完整 YAML；Dynamic Stage 允许“稳定 Pipeline 骨架 + 运行时生成的可变 Stage”。生成的 YAML 仍受 Pipeline 权限、OPA、Secret 和 Connector 模型约束。[Dynamic Pipeline Execution](https://developer.harness.io/docs/platform/pipelines/dynamic-execution-pipeline/)

因此，“智能工作流编排”最值得强调的不是 Pipeline 变得不可见，而是：

> **Pipeline 从只编排固定脚本，扩展为同时编排确定性 Step 和推理型 Agent Step；Agent 可以修改流程定义，但生产执行仍落在显式 Pipeline、审批和失败处理结构中。**

当前证据仍不能支持以下更强说法：

- Pipeline 会在每次执行中由 Agent 自动从零生成；
- Harness 内置 Agent 会自行决定何时使用 Dynamic Execution，并不受约束地增删生产 DAG 节点；
- Agent 的判断可以绕过 Test、Approval、OPA 或 Rollback；
- 所有跨模块工作流已经统一到同一 Runtime。

准确的边界是：**Harness 提供“可动态生成”的执行原语，但由谁生成、何时生成以及生成逻辑是否可靠，仍由客户工作流或上层 Agent 负责。**

## 四、与 Harness AI 其他能力的关系

### 4.1 Worker Agents

Worker Agent 是 Intelligent Workflow Orchestration 中的 **推理型执行单元**，不是替代 Pipeline 的总控 Agent。

- Agent Definition 集中保存 Instructions、Model Connector、可选 MCP、输入和输出；
- Pipeline 中的 Agent Step 引用 Agent 名称和版本；
- Agent 可以顺序、并行、条件或矩阵组合；
- Approval、Failure Strategy、Retry 和 Rollback 对 Agent Step 与其他 Pipeline Step 采用同一控制模式。[Worker Agent Configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/)、[Worker Agents 发布说明](https://www.harness.io/blog/introducing-autonomous-worker-agents)

### 4.2 DevOps Agent

DevOps Agent 位于 Harness UI，负责 **创建和修改编排定义及其资源**：

- 创建/修改 Step、Stage 和 Pipeline；
- 创建多模块 Pipeline；
- 生成 OPA Rego Policy；
- 创建 Service、Environment、Connector 和 Secret；
- 管理 GitOps Application、Cluster 和 Repository 等资源。[DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/)

DevOps Agent 的智能输出仍受到用户自身 RBAC 约束；AI 生成的 Pipeline 在保存或运行时可以被 OPA 拦截。Harness 官方还将 AI 生成资源标记和 Audit Trail 作为可追责机制，但这些属于厂商公开的产品治理说明，不能替代目标账户上的实际事件覆盖验证。[Harness AI Governance Update](https://www.harness.io/blog/harness-ai-december-2025-updates)、[OPA Overview](https://developer.harness.io/docs/platform/governance/policy-as-code/harness-governance-overview/)

### 4.3 Knowledge Graph / HQL

Knowledge Graph/HQL 是 **编排前和编排中的结构化理解层**，不是工作流引擎。

- 对已建模数据，Harness 主张优先用 Knowledge Graph + HQL 做 Read / Query / Analyze；
- Schema、字段类型、聚合能力和跨模块关系约束 Agent 的查询；
- 这让 Agent 更可靠地理解 Pipeline、Service、Deployment、Security 和 Incident 之间的关系；
- 对日志、Trace 和未建模外部系统，再通过 Scoped Retrieval、Integration 或 MCP 补充。[Knowledge Graph + HQL](https://www.harness.io/blog/why-harness-ai-uses-knowledge-graph)

在架构图中，它应当位于 Agent 与编排层之前，表达“给决策提供上下文”，而不能与 Approval、Rollback 混为一类。

### 4.4 Pipeline Context

Pipeline Context 是 **本次执行事实**：

- Trigger、Repository、Branch、Input 和变量；
- 前序 Step 的输出；
- 执行状态、日志和 Workspace 文件；
- Harness Expression 在 Pipeline 运行时解析并注入 Agent Instructions。[Worker Agent Configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/)、[VS Code Extension](https://developer.harness.io/docs/platform/harness-ai/code-agent)

它与 Knowledge Graph 的区别是：

> Knowledge Graph/HQL 提供组织级、跨模块、持续更新的交付知识；Pipeline Context 提供某一次运行的局部状态和数据流。

### 4.5 MCP / Connector

MCP 与 Connector 是 **Tool 与行动通道**：

- Harness MCP Server 用少量通用 Tool 覆盖跨模块资源的 List、Get、Create、Update、Delete 和 Execute；
- `harness_execute` 可以运行/重试 Pipeline、批准/拒绝 Approval、同步 GitOps Application 或切换 Feature Flag；
- Connector 保存目标系统连接信息和凭据；
- MCP 客户端的确认能力并不一致，且可以通过配置跳过确认，因此“Tool 可调用”不能等同于“操作已获授权”。[Harness MCP Server](https://developer.harness.io/docs/platform/harness-ai/harness-mcp-server/)

Pipeline 将 Tool 调用放进可观察的执行上下文，但具体权限还需由 RBAC、Scoped Token、Connector Allowlist 和外部系统权限共同限制。

### 4.6 Approval / OPA / Failure Strategy / Rollback

这些能力是 Intelligent Workflow Orchestration 的 **控制语义**，而不是 Agent 自己的 Prompt 规则：

- **Approval**：在 Stage 之间或 Stage 内阻止流程继续，直到指定用户组批准或拒绝。[Harness Approval](https://developer.harness.io/docs/platform/approvals/adding-harness-approval-stages/)
- **OPA**：可以在 Pipeline On Save、On Run 和 On Step Start 事件执行策略；Error and Exit 会阻断动作。On Step Start 当前仍需 Feature Flag。[OPA Overview](https://developer.harness.io/docs/platform/governance/policy-as-code/harness-governance-overview/)
- **Failure Strategy**：对不同错误选择 Retry、Manual Intervention、Abort、Rollback 等动作，并可定义重试耗尽后的处理。[Failure Strategies](https://developer.harness.io/docs/platform/pipelines/failure-handling/define-a-failure-strategy-on-stages-and-steps/)
- **Rollback**：Pipeline Rollback 默认适用于 CD Step/Stage；其他 Stage 需要 `CDS_ALLOW_EXPRESSION_RESOLUTION_PIPELINE_ROLLBACK` Feature Flag，且 Parent Pipeline 回滚不会自动回滚独立执行的 Child Pipeline。[Pipeline Rollback](https://developer.harness.io/docs/platform/pipelines/failure-handling/define-a-failure-strategy-for-pipelines/)

这说明 Agent 可以产生概率性的判断和动作，但流程是否继续、失败后如何恢复，仍由显式控制结构决定。

## 五、产品状态与证据边界

### 5.1 不能给 Intelligent Workflow Orchestration 整体标一个状态

截至 2026-07-24：

| 组成能力 | 官方状态或边界 |
|---|---|
| Harness Agents / Worker Agents | Harness AI 状态表列为 Generally Available |
| DevOps Agent 与多模块 Pipeline 创建 | Generally Available |
| MCP Server | Generally Available |
| AI Test Copilot、FME Release Agent、AI Scribe | 状态表列为 Generally Available，但账户许可、Support 或模块条件仍需分别确认 |
| 传统顺序/并行 Pipeline、条件、Approval、Failure Strategy | 成熟平台能力 |
| DAG Pipeline | Beta，需 `PIPE_ENABLE_DEPENDENCY_BASED_EXECUTION` |
| Dynamic Pipeline Execution | 需 `PIPE_DYNAMIC_PIPELINES_EXECUTION`；仅 API；同时需要 Account 与 Pipeline 设置 |
| Dynamic Stage | 需 `PIPE_DYNAMIC_STAGE_EXECUTION` |
| OPA On Step Start | 需 `PIPE_IS_PRE_STEP_OPA_POLICY_EVALUATION_ENABLED` |
| 非 CD Stage 的 Pipeline Rollback | 需 `CDS_ALLOW_EXPRESSION_RESOLUTION_PIPELINE_ROLLBACK` |
| Worker Agent Scoped Token | 需 `HARNESS_TOKEN_INJECT`，当前实体覆盖有限 |

状态来源：[Harness AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/)、[DAG Pipelines](https://developer.harness.io/docs/platform/pipelines/dag-pipelines/)、[Dynamic Pipeline Execution](https://developer.harness.io/docs/platform/pipelines/dynamic-execution-pipeline/)、[Dynamic Stage](https://developer.harness.io/docs/platform/pipelines/add-a-stage/)、[OPA Overview](https://developer.harness.io/docs/platform/governance/policy-as-code/harness-governance-overview/)、[Pipeline Rollback](https://developer.harness.io/docs/platform/pipelines/failure-handling/define-a-failure-strategy-for-pipelines/)、[Agent Permissions](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/)

### 5.2 证据局限

1. **IWO 是产品定位，不是公开参考架构。** 官方产品页给出定义和用例，但没有一份专门的 Developer Hub 文档描述其统一对象模型、调度 API、状态机或 SLA。
2. **动态 Pipeline 原语不等于通用自治重规划器。** 当前文档证明外部系统能通过 API 提交动态 YAML，前序 Step 也能生成 Dynamic Stage；但没有证明 Harness 内置 Agent 会普遍、自主、安全地重写整个生产执行图。
3. **“跨模块”是能力组合，不等于状态和 Runtime 统一。** AI Test、FME、AI SRE、Worker Agent 和 DevOps Agent 的模型、权限、触发方式与执行环境不同。
4. **治理与动态能力存在启用条件和范围限制。** DAG、Dynamic Execution、Dynamic Stage、OPA On Step Start、非 CD Pipeline Rollback、Scoped Token 均有 Beta 或 Feature Flag 边界。
5. **效果数字主要来自厂商案例或厂商研究。** 它们可以证明采用信号，不能外推为所有客户在复杂跨模块 Agent 工作流中的稳定性、成功率或单位成本。

综合置信度：

- 官方定位与组成机制：**high**
- “Pipeline 是主要确定性编排骨架”的分析判断：**high**
- 跨模块能力已统一到单一工作流 Runtime：**low / 不支持**
- 生产执行时通用动态重规划：**low / 未证实**
- 所有账户无条件具备完整治理能力：**low / 不支持**

## 六、适合单页 PPT 的五条洞察

### 洞察一：Harness 的“智能编排”不是让 Agent 取代 Pipeline，而是让 Pipeline 同时承载确定性 Step 与推理型 Step

Worker Agent 作为一等 Pipeline Step 进入既有顺序、并行、条件、审批、失败处理和回滚结构。Pipeline 从脚本执行器升级为概率决策的生产控制结构。

### 洞察二：Harness 形成了双向关系——Agent 生成和优化流程，Pipeline 反过来治理 Agent

DevOps Agent 能创建、修改和排障多模块 Pipeline；Dynamic Pipeline/Stage 还允许外部 Agent 或程序按运行上下文生成部分流程；Worker Agent 又被 Pipeline 版本化、组合、授权和审计。智能不只发生在“流程里面”，也开始改变“流程本身”。

### 洞察三：真正的差异不是多 Agent，而是把跨模块上下文、行动与治理放进同一作业闭环

Knowledge Graph/HQL 提供平台级知识，Pipeline Context 提供本次运行事实，MCP/Connector 提供行动通道，Pipeline 连接测试、发布、安全、审批与恢复。

### 洞察四：智能工作流的核心边界是“Agent 决定候选行动，控制结构决定能否执行和如何恢复”

Prompt 和 AI Rules 只能指导行为；RBAC、OPA、Approval、Failure Strategy、Rollback 以及外部 Test/Scan/SLO 才是生产流程的硬约束与结果判定。

### 洞察五：当前形态是“稳定骨架 + 受控动态部分”，还不是统一自治工作流操作系统

多模块能力已经能够被 Pipeline、IDP Workflow 和 Runbook 连接，Dynamic Stage 允许在运行时追加受治理的可变流程；但不同领域 Agent 仍有不同 Runtime、权限和成熟度。IWO 目前更适合作为平台架构定位，而不是单独采购或单独评估的产品。

## 七、对当前单页架构图的影响

### 7.1 左侧图的建议

不需要新增一个与 Pipeline 并列的“智能编排”层，而应把当前架构图的第 05 层“交付流程编排”改名并补充内涵：

```text
05  跨模块智能工作流编排

Agent 生成与优化流程  │  Pipeline 执行与治理

跨模块 Stage / Step · 顺序 / 并行 / DAG / 动态 Stage*
条件 / 循环 / 矩阵 · Approval · OPA
Retry / Failure Strategy · Rollback
```

`* Dynamic Pipeline / Dynamic Stage 当前需要 Feature Flag。`

图中关系应表达为：

```text
知识图谱 + HQL       Pipeline Context       MCP / Connector
       \                   |                   /
        \             三类 Agent              /
         \                 |                  /
          ───── 跨模块智能工作流编排 ─────
             Agent Step + 确定性 Step
                         |
                  Harness Cloud / Delegate /
                  Customer Kubernetes Runtime
```

图上需要保留一个边界说明：

> **Worker Agent 作为 Pipeline Step 执行；领域专用 Agent 可由 Pipeline 触发，也可能运行在模块原生工作流中。**

### 7.2 右侧说明的建议

右侧可增加一块“智能工作流编排”，使用以下短文案：

**智能生成，确定性执行**

DevOps Agent 根据组织上下文创建和优化跨模块 Pipeline；Worker Agent 作为推理型 Step，与测试、扫描、部署和领域动作共同执行。

**上下文驱动流程**

Knowledge Graph/HQL 提供平台级知识，Pipeline Context 提供本次运行事实，MCP/Connector 将判断转化为 Harness 或第三方系统动作。

**统一治理行动**

Pipeline 用顺序、并行、条件、审批、OPA、失败策略与回滚约束 Agent 行动，使智能决策进入可见、可审计、可恢复的交付流程。

右侧总结句建议为：

> **Agent 让流程会理解、会判断、会行动；Pipeline 让这些行动有顺序、有边界、可批准、可失败、可恢复。**

## 八、官方一手资料索引

- [Harness AI 产品页：三个支柱及 Intelligent Workflow Orchestration 定义](https://www.harness.io/products/harness-ai)
- [Harness AI Overview：功能状态与跨模块 AI 能力](https://developer.harness.io/docs/platform/harness-ai/overview/)
- [Harness AI Core Capabilities](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/)
- [DevOps Agent：多模块 Pipeline、OPA、资源与 GitOps](https://developer.harness.io/3k-docs/ai/devops-agent/)
- [Worker Agents 发布说明：Agent Step、组合、治理与 Marketplace](https://www.harness.io/blog/introducing-autonomous-worker-agents)
- [Worker Agent Configuration：Instructions、Inputs、Pipeline Context、MCP](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/)
- [Agent Permissions：Scoped Token 与当前限制](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/)
- [Knowledge Graph + HQL：结构化查询与四层数据访问模型](https://www.harness.io/blog/why-harness-ai-uses-knowledge-graph)
- [Harness MCP Server：跨模块 Tool 与 Resource Registry](https://developer.harness.io/docs/platform/harness-ai/harness-mcp-server/)
- [Harness Platform：Pipeline 与共享平台能力](https://developer.harness.io/docs/platform/)
- [DAG Pipelines：依赖式执行与 Beta 状态](https://developer.harness.io/docs/platform/pipelines/dag-pipelines/)
- [Dynamic Pipeline Execution：运行时 YAML 与治理边界](https://developer.harness.io/docs/platform/pipelines/dynamic-execution-pipeline/)
- [Dynamic Stage：运行中追加 Stage/Step](https://developer.harness.io/docs/platform/pipelines/add-a-stage/)
- [Dynamic Pipelines 官方说明：稳定骨架与动态部分](https://www.harness.io/blog/harness-dynamic-pipelines-complete-adaptability-rock-solid-governance)
- [Approval Stage](https://developer.harness.io/docs/platform/approvals/adding-harness-approval-stages/)
- [OPA Policy As Code](https://developer.harness.io/docs/platform/governance/policy-as-code/harness-governance-overview/)
- [Failure Strategies](https://developer.harness.io/docs/platform/pipelines/failure-handling/define-a-failure-strategy-on-stages-and-steps/)
- [Pipeline Rollback](https://developer.harness.io/docs/platform/pipelines/failure-handling/define-a-failure-strategy-for-pipelines/)
- [AI Test in Harness CI/CD](https://developer.harness.io/docs/ai-test-automation/integrations/harness-cd/)
- [FME Steps in Harness Pipelines](https://developer.harness.io/docs/feature-management-experimentation/pipelines/)
- [IDP Self Service Workflows](https://developer.harness.io/3k-docs/internal-developer-portal/flows/overview/)
- [AI SRE Runbooks](https://developer.harness.io/docs/ai-sre/runbooks/)
- [AI for GitOps：Agent 修改和优化 Pipeline 的官方示例](https://www.harness.io/blog/ai-for-gitops-tame-your-argo-sprawl)
- [Harness 公司战略：Knowledge Graph、Focused Agents、Reliable Orchestration Engine](https://www.harness.io/blog/240m-financing-to-bring-ai-to-everything-after-code)
- [Harness 对 Pipeline 与 Agent 分工的官方表述](https://www.harness.io/blog/ai-writes-the-code-who-delivers-it-safely)
- [Release Orchestration：需要与 IWO 区分的独立发布管理产品](https://www.harness.io/blog/introducing-harness-release-orchestration)
