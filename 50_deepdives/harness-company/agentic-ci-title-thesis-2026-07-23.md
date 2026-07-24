---
title: Harness Agentic CI 单页主张与标题核验
aliases:
  - Harness Agentic CI Title Thesis 2026-07-23
tags:
  - research/agentic-cicd
  - research/deep-dive
  - research/validation
  - company/harness
  - competitive-analysis
status: complete
as_of: 2026-07-23
topic_id: harness-company
topic_type: company
scope: agentic-ci-title-thesis
source_policy: official-primary-only
confidence: medium-high
presentation_ready: true
---

# Harness Agentic CI 单页主张与标题核验

> [!abstract] 推荐结论
> **最终标题：Harness CI：将会推理、能行动、可治理的 Agent 嵌入交付 Pipeline**
>
> 该标题同时表达三件事：Agent 基于交付上下文进行推理，通过 Tool 采取行动，并在 Pipeline、权限与治理边界内运行。Harness 的核心机制不是拥有更聪明的通用模型，而是把**可复用、可版本化的 Worker Agent Definition**作为原生 `Agent` Step 放进真实的 CI/CD/IaCM/STO/SCS/Custom Pipeline，让 Agent 的结构化输出继续被条件、OPA、Approval、Failure Strategy、Test/Scan/Signature/SLO 和 Rollback 消费，并可运行在 Harness Cloud 或客户 Kubernetes。
>
> 横向校验同时表明：安全地在 CI 中运行 Agent、自动修失败、支持客户 Runner 或多模型都不是 Harness 独有。真正可辩护的差异是：**Agent 是交付图中的一个受控执行单元，而不是与 Pipeline 平行的聊天助手、一次独立修复会话，或只面向 Flaky Test 的专项服务。**

本文件只为 Harness CI 单页提炼主张，不修改 Presentation 或 PPT。架构事实详见 [[50_deepdives/harness-company/architecture-validation-2026-07-23|Harness 架构核验]] 与 [[50_deepdives/harness-company/architecture-content-alignment-2026-07-23|Harness 架构内容对齐核验]]。

## 一、Harness 的事实底座

### 1.1 Worker Agent 是真正 Pipeline-native 的部分

| 事实 | 页面意义 | 官方证据 |
|---|---|---|
| Worker Agent 把 Instructions、Model Connector、可选 MCP、Inputs/Environment/Outputs 封装成可复用定义 | Agent 不是 Pipeline YAML 中的一段临时 Prompt，而是独立管理的执行单元 | [Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) |
| Pipeline 用 `agentName: name@version` 引用 Agent，可固定版本或选择 Stable Version | Agent 行为可以像其他交付依赖一样被版本化和升级 | [Worker Agents - Use an Agent Step](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) |
| 同一个 `Agent` Step 可进入 CI、CD、IaCM、STO、SCS、Custom Stage；CD/Custom 必须位于 Containerized Step Group | Harness 的 Agentic CI 不是止于 PR 修复，而是能进入更长的生产交付链 | [Worker Agents - Supported Stage Types](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) |
| Agent Output Variable 可被下游 Step、条件、通知和 Gate 消费 | 概率输出能够被转换为显式 Pipeline Contract，而不是只留在 Chat/Comment 中 | [Worker Agent Outputs](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)、[Agent Examples](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/example-agents/) |
| OPA 可在 Agent Definition 保存和 Pipeline 运行时检查 Model、MCP、`max_turns`、Permission 等配置 | 治理对象不只是最终代码，也包括 Agent 自身的定义和使用方式 | [Worker Agents - Policy Governance](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)、[Harness Policy as Code](https://developer.harness.io/docs/platform/governance/policy-as-code/harness-governance-overview/) |
| Worker 可运行在 Harness Cloud，或通过 Delegate 运行于客户 Kubernetes；Cloud 仅明确支持 CI/STO/SCS/IaCM | Runtime 可靠近客户网络与数据，但不是任意 Stage 都共享相同 Cloud Runtime | [Worker Agents - Infrastructure and Execution](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) |
| Pipeline 继续提供顺序/并行、Approval、Failure Strategy、Retry、Rollback 和下游 Test/Scan/Verify | Agent 的判断和动作被现有确定性控制面包围 | [Pipeline Step Groups](https://developer.harness.io/docs/platform/pipelines/use-step-groups/)、[Approval](https://developer.harness.io/docs/platform/approvals/adding-harness-approval-stages/)、[Failure Strategies](https://developer.harness.io/docs/platform/pipelines/failure-handling/define-a-failure-strategy-on-stages-and-steps/) |

### 1.2 不能把全部 Harness Agent 都写成 Pipeline-native

| Agent 类型 | 准确位置 | 页面边界 | 官方证据 |
|---|---|---|---|
| DevOps Agent | Harness UI 中创建/修改 Pipeline 和资源、生成 OPA、分析失败 | 使用 Harness Managed Model，不支持 BYOM；不是运行于 Pipeline 内的 Agent Step | [DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/)、[Core Capabilities](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/) |
| Worker Agents | 作为原生 `Agent` Step 执行于 Pipeline | 本页“进入生产交付、由 Pipeline 控制”的直接主语 | [Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) |
| Code/AI Test/Security/FME/AI SRE 专项 Agent | 各自模块中的 Run Step、Pod、Chatbot、Incident Service 或其他专用 Runtime | 可作为场景和结果举例，但不能统一套用 Worker 的版本、权限、Model Connector 和 Runtime 结论 | [Code Agents](https://developer.harness.io/3k-docs/ai/code-agent/)、[AI Test](https://developer.harness.io/docs/ai-test-automation/get-started/overview/)、[Security Agents](https://developer.harness.io/3k-docs/ai/security-agent/)、[FME Release Agent](https://developer.harness.io/docs/feature-management-experimentation/release-agent/)、[AI SRE](https://developer.harness.io/docs/ai-sre/ai-agent/) |

因此，页面应写“**Harness 以 Worker Agent 为原生 Pipeline Step，同时由 DevOps Agent 与专项 Agent 覆盖设计面和领域场景**”，不应写“所有 Harness Agent 都运行在 Pipeline 中”。

## 二、业界横向校验

### 2.1 对比矩阵

| 方案 | Agent 与 CI/Pipeline 的关系 | 治理与 Runtime | 对 Harness 主张的反证或衬托 | 官方证据 |
|---|---|---|---|---|
| **Harness Worker Agents** | 独立 Catalog Definition，以 `name@version` 作为 `Agent` Step 嵌入同一条多 Stage 交付 Pipeline；输出直接进入下游表达式和 Gate | OPA 可检查 Agent Save/Run；Pipeline 有 Approval/Failure/Rollback；Harness Cloud 或客户 Kubernetes + Delegate | 辨识度在“版本化 Step + 输出 Contract + 既有交付控制面”的组合，不只是 CI 修复 | [Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)、[Agent Examples](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/example-agents/) |
| **GitHub Actions / Copilot** | GitHub Agentic Workflow 用 Markdown 定义并编译为加固的 `.lock.yml` Actions Workflow；Copilot Cloud Agent 在 Actions 驱动的临时环境中修改、测试并产出 PR | Agentic Workflow 默认只读、声明 Safe Outputs、隔离 Secret、Firewall、Threat Detection，并支持 `max-ai-credits`；Copilot 也可使用 Self-hosted Runner | 反证“Agent 进入 CI”“多模型”“客户 Runner”“安全执行”是 Harness 独有。Harness 的相对差异应写成：Agent 是更长交付 Pipeline 中可插拔的版本化 Step，而非把整个 Repository Automation 定义成 Agentic Workflow | [GitHub Agentic Workflows](https://docs.github.com/en/copilot/concepts/agents/about-github-agentic-workflows)、[Copilot Agent Environment](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment) |
| **GitLab Duo Agent Platform** | Fix CI/CD Pipeline Flow 已 GA，可分析失败并给出 Suggestion/MR；Custom Flow 由一个或多个 Agent 组成。UI 发起的 Flow 通过独立、临时 Workload Pipeline 执行 | Composite Identity、默认只访问 GitLab 的 Network Policy、Hosted/Own Runner、Custom Image；Tool Governance 与 AI Audit 仍为 Beta，且 Tool Governance 当前不约束 CI/CD Runner 中的 Flow | 反证“跨 SDLC Agent Platform”“CI 自动修复”“客户 Runner”是 Harness 独有。Harness 的相对差异是 Worker 直接位于目标交付 Pipeline 内，而 GitLab Flow 是独立 Agent Session/Workload Pipeline | [GitLab Agent Platform](https://docs.gitlab.com/user/duo_agent_platform/)、[Fix CI/CD Pipeline Flow](https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/fix_pipeline/)、[Flow Execution](https://docs.gitlab.com/user/duo_agent_platform/flows/execution/)、[Tool Governance](https://docs.gitlab.com/user/duo_agent_platform/agents/tool-governance/) |
| **CircleCI Chunk** | Cloud 组织中的专项 CI Agent，通过 Chat、Task、Scheduler 和失败页修复入口分析 Pipeline 历史与代码，提出 Diff/Commit/PR，并可反复验证；不是任意 Pipeline 中的通用 Agent Step | `cci-agent-setup.yml` 复用 CircleCI Executor/Resource Class/Context；模型可由 CircleCI、Anthropic、OpenAI、Bedrock 提供；当前总体为 Beta | 反证“自愈 CI”“修 Flaky Test”“Agent 验证后开 PR”是 Harness 独有。CircleCI 在 Inner-loop Sidecar/Microbuild 和专项验证上更聚焦；Harness 的差异是通用 Step 与跨交付阶段编排 | [Chunk Overview and Setup](https://circleci.com/docs/guides/toolkit/chunk-setup-and-overview/)、[Chunk Sidecars](https://circleci.com/blog/chunk-sidecars/)、[Fix Flaky Tests](https://circleci.com/blog/fix-flaky-tests-with-chunk/) |

### 2.2 横向结论

以下说法**不能**作为 Harness 独有主张：

1. “Agent 能在 CI 中运行”——GitHub Agentic Workflows、GitLab Flows 和 CircleCI Chunk 都成立；
2. “Agent 能自动修失败并提 PR”——GitLab Fix Pipeline、GitHub Cloud Agent、CircleCI Chunk 都成立；
3. “支持多模型或客户 Runtime”——GitHub 支持多个 Engine 与 Self-hosted Runner，GitLab 支持 Self-managed Model/Runner，CircleCI Chunk 支持多 Model Provider；
4. “具有企业治理”——GitHub 已有 Read-only、Safe Outputs、Secret Isolation 和 Threat Detection；GitLab 已有 Composite Identity、Network Policy，并在补齐 Tool Governance/Audit；
5. “构成闭环验证”——CircleCI Chunk 的 Sidecar/Microbuild 在 Agent Inner Loop 中直接把失败反馈给 Coding Agent。

Harness 相对更有辨识度的是四项组合：

1. **Step-level embedding**：Agent 不是一条平行自动化或独立修复 Session，而是目标交付 Pipeline 图中的原生 Step；
2. **Versioned reusable definition**：Prompt、Model、MCP、Input/Output 与 Runtime Definition 集中在 Catalog，并由 Pipeline 固定到 `agent@version`；
3. **Output-to-gate contract**：Agent Output 进入下游条件、Approval、Policy、Test/Scan/Verify，而不是把 Agent 自评当成正确性证明；
4. **Delivery-stage breadth**：相同机制覆盖 CI、CD、IaC、STO、SCS 和 Custom Stage，并能在 Harness Cloud 或客户 Kubernetes 执行。

这不是绝对的“技术独占”，而是截至 2026-07-23 在所比较方案中最清楚、最成熟的**产品化组合差异**。

## 三、推荐的 Agent、Pipeline、治理与 Runtime 概括

### 3.1 一句话机制

**Agent 生成候选判断与动作；Pipeline 编排顺序、权限、门禁与失败处理；Test/Scan/Policy/Signature/SLO 证明结果；Harness Cloud 或客户 Kubernetes 承载执行。**

### 3.2 四层最简结构

| 层 | 页面用语 | 不应越界的说法 |
|---|---|---|
| Agent | `Versioned Worker Agent — Instructions · Model · MCP · Inputs/Outputs` | 不说所有 Harness Agent 都 Pipeline-native |
| Pipeline | `Deterministic Control — DAG · Conditions · Approval · Failure · Rollback` | 不说 Pipeline 能把概率输出变成必然正确 |
| Governance / Oracle | `RBAC · OPA · Test · Scan · Signature · SLO · Human Review` | 不把 Audit 当 Gate，不把 Agent 自评当 Oracle |
| Runtime | `Harness Cloud │ Customer Kubernetes + Delegate` | 不把 Delegate 画成 Agent Runtime；Cloud Stage 范围有限 |

### 3.3 单页副标题

`Worker Agent 以 name@version 进入交付图；结构化输出交给条件、OPA、Approval 与 Test/Scan/SLO 决定晋级、阻断或回滚。`

## 四、反例与边界

| 边界 | 为什么必须保留 | 官方证据 |
|---|---|---|
| DevOps Agent 与专项 Agent 并非都在 Pipeline 内 | 推荐标题直接描述的是 Worker Agent 产品机制，其他 Agent 只能作为组合补充 | [Core Capabilities](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/) |
| Scoped Token 仍有 Feature Flag、Trigger Gap 和官方语义冲突 | 不能写“所有 Agent 已统一实现最小权限继承” | [Worker Permissions](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)、[Agent Permissions](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/) |
| Agent Output 仍是待验证的候选结果 | Output Variable 可驱动 Gate，但其内容正确性必须由下游确定性 Oracle 验证 | [Agent Examples](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/example-agents/) |
| Harness Cloud Runtime 不是任意 Stage 通用 | Cloud 仅明确支持 CI/STO/SCS/IaCM；CD/Custom 需 Containerized Step Group | [Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) |
| 自愈不是 Harness 的独占优势 | GitLab Fix Pipeline 与 CircleCI Chunk 已有正式能力，GitHub Agent 也能修改、测试和产出 PR | [GitLab Fix Pipeline](https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/fix_pipeline/)、[CircleCI Chunk](https://circleci.com/docs/guides/toolkit/chunk-setup-and-overview/)、[GitHub Cloud Agent](https://docs.github.com/en/copilot/concepts/agents/cloud-agent) |
| “确定性控制”不等于“确定性结果” | Pipeline 只能确定执行结构和 Gate；Test、Scan、Policy 与 SLO 也只证明被定义和被观测的部分 | [STO Failure Criteria](https://developer.harness.io/docs/security-testing-orchestration/key-concepts/fail-pipelines-by-severity/)、[SLO Rollback](https://developer.harness.io/docs/service-reliability-management/manage-slo/automated-deployment-rollback/) |

## 五、标题候选

### 候选 1｜最终采用

**Harness CI：将会推理、能行动、可治理的 Agent 嵌入交付 Pipeline**

- 优点：突出 Agent 的智能化、行动能力和生产治理，同时明确 Pipeline-native 的产品形态；
- 精确边界：“嵌入交付 Pipeline”的直接主语是 Worker Agents；DevOps Agent 与领域专用 Agent 作为组合能力在图中分别说明；
- 相比“Agent 进入生产交付，Pipeline 承担确定性控制”更抓眼球，也更适合与上下文、Tool 和治理架构图配合。

### 候选 2｜控制关系最直接

**Harness CI：让 Agent 进入生产交付，让 Pipeline 保留确定性控制**

- 优点：直接说明 Agent 与 Pipeline 的控制关系；
- 风险：对 Agent 如何体现智能、如何行动表达不足。

### 候选 3｜机制最精确

**Harness CI：把概率 Agent 封装成可版本化、可门禁的 Pipeline Step**

- 优点：直接命中 `agent@version`、Output Contract 和下游 Gate；
- 风险：“概率 Agent”对非技术管理者略抽象，“可门禁”需要副标题解释 Oracle。

### 候选 4｜观点最强

**Agent 负责候选动作，Pipeline 决定能否晋级**

- 优点：短、清楚，适合在图上方做大标题；
- 风险：没有直接点出 Harness，也不是 Harness 独有，建议配 `Harness Agentic CI` 眉题。

### 候选 5｜突出差异

**Harness 的差异不在“AI 修 CI”，而在 Agent 成为交付图中的受控 Step**

- 优点：主动避开 GitLab/CircleCI 已经具备的自愈叙事；
- 风险：语气偏辩论式，“受控”需要治理和 Runtime 证据支撑。

### 候选 6｜架构表达

**从 Agent Catalog 到 Outcome Oracle：Harness 把自治能力纳入确定性交付链**

- 优点：适合技术架构图，能容纳 Version、Runtime、Policy 与 Oracle；
- 风险：“自治能力”可能被理解为全部专项 Agent；“Outcome Oracle”需要现场解释。

## 六、最终推荐文案

**标题：** `Harness CI：将会推理、能行动、可治理的 Agent 嵌入交付 Pipeline`

**页面解释：** `三类智能体基于 Knowledge Graph、HQL 与 Pipeline Context 理解交付现场，通过 MCP、Connector 和 API 采取行动，再由 Pipeline、权限与治理边界约束执行。`

**页脚边界：** `“确定性”指控制流与门禁，不表示 Agent 输出或软件结果必然正确；DevOps Agent 与专项 Agent 不共享同一 Pipeline Runtime。`
