---
title: Harness 架构图内容对齐核验
aliases:
  - Harness Architecture Content Alignment 2026-07-23
tags:
  - research/agentic-cicd
  - research/deep-dive
  - research/validation
  - company/harness
  - architecture
status: complete
as_of: 2026-07-23
topic_id: harness-company
topic_type: company
scope: presentation-architecture-content-alignment
source_policy: official-primary-only
confidence: medium-high
presentation_ready: true
---

# Harness 架构图内容对齐核验

> [!abstract] 结论
> 将原来的 `Outcome / Feedback` 改成最上方的**“场景与应用”**并画成上下行闭环，语义成立，而且比把 Outcome 画成一个孤立底层更准确：代码、测试、安全、发布和事件响应场景既产生 Intent/Event，也接收 PR、Test Result、Remediation、Experiment Summary、RCA Theory 等结果。
>
> 但这不是 Harness 官方发布的参考架构，而是基于官方产品能力重构的分析图。闭环必须表达**业务与控制反馈**，不能暗示模型会根据结果自动训练或自我改进。RBAC、Secrets、OPA、Audit 更适合画成贯穿多层的 Trust Rails；Approval 属于 Pipeline 内的确定性 Gate；Scoped Token 属于 Pipeline Control 到 Worker Runtime/Tool 的权限边界，并且当前仍有 Feature Flag、Trigger Gap 和官方文档冲突。

本文件是 [[50_deepdives/harness-company/architecture-validation-2026-07-23|Harness 2026 产品组合与 Agentic Software Delivery 架构核验]] 的页面内容对齐补充，只回答五个画图问题，不修改 Presentation，也不生成图片。

## 一、证据口径

- 仅使用 Harness Developer Hub、Harness 官方产品/工程文章和 Harness 官方 GitHub 仓库；
- 产品状态以 2026-07-23 可见的 [Harness AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/) 为主；
- “支持”表示能由官方产品资料直接证明；“分析判断”表示由多项官方机制共同支撑，但不是 Harness 原生术语；
- “可审计”“可治理”和“有 Oracle”不等于默认完整开启，也不等于任意账户已经完成生产验收。

## 二、问题一：将 Outcome / Feedback 改为最上方“场景与应用”，并画 Loop 是否成立

### 2.1 结论：成立，但要画成双向业务闭环

Harness 官方将 AI 能力分为 Harness UI、IDE 和 Pipeline 三个工作面，并描述从开发、Pipeline 配置到执行的端到端工作流；Worker Agent 的输出又可以作为后续 Step 的输入或条件。Pipeline Chaining、SLO-driven Rollback 和 AI SRE 的 Theory Re-evaluation 进一步证明，结果可以回到下一次触发、判断或执行。因此把“场景与应用”放在最上方，同时让它成为**需求/事件的起点**和**结果/证据的终点**，语义成立。

证据：[Core Capabilities](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/)、[Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)、[Pipeline Chaining](https://developer.harness.io/docs/platform/pipelines/pipeline-chaining/)、[SLO-driven Rollback](https://developer.harness.io/docs/service-reliability-management/manage-slo/automated-deployment-rollback/)、[RCA Change Agent](https://developer.harness.io/3k-docs/ai-sre/ai-agent/rca-change-agent/)。

### 2.2 顶部“场景与应用”不应只有一个抽象 Outcome

| 场景 | 向下输入 | 向上返回 | 官方证据 |
|---|---|---|---|
| Code / PR | Commit、PR、Review Intent | PR Comment、Test、Fix Branch/PR | [Code Quality Agents](https://developer.harness.io/3k-docs/ai/code-agent/)、[Code Repository AI Agents](https://developer.harness.io/docs/code-repository/pull-requests/ai-agents/) |
| Build / Test | Build/Test Trigger、自然语言 Test Intent | Test Run、Pass/Fail、Failure Triage | [AI Test Overview](https://developer.harness.io/docs/ai-test-automation/get-started/overview/)、[AI Test in CD](https://developer.harness.io/3k-docs/ai-test-automation/integrations/harness-cd/) |
| Security | Scan Finding、Severity/Policy Criteria | Remediation、Code Suggestion、Fix PR | [STO AI Remediation](https://developer.harness.io/docs/security-testing-orchestration/remediations/ai-based-remediations/)、[Security Agents](https://developer.harness.io/3k-docs/ai/security-agent/) |
| Release / FME | Flag、Metric、Experiment Question | Answer、Metric/Experiment Summary | [FME Release Agent](https://developer.harness.io/docs/feature-management-experimentation/release-agent/) |
| Incident / SRE | Alert、Incident Communication、Change Event | Timeline、Key Event、Action Item、RCA Theory、Runbook Result | [AI Scribe](https://developer.harness.io/docs/ai-sre/ai-agent/)、[RCA Change Agent](https://developer.harness.io/3k-docs/ai-sre/ai-agent/rca-change-agent/)、[Runbooks](https://developer.harness.io/docs/ai-sre/runbooks/) |

### 2.3 推荐画三种 Loop，不画“模型自学习”

| Loop | 推荐箭头文字 | 表达的系统语义 |
|---|---|---|
| Demand / Event Loop | `Intent · Commit · Scan Finding · Metric · Incident` ↓ | 人或系统事件启动 Agent、Tool 或 Pipeline 工作 |
| Outcome / Evidence Loop | `PR · Test · Remediation · Summary · RCA · Evidence` ↑ | 产物和证据回到应用场景，被人、触发器或下一条 Pipeline 消费 |
| Control / Recovery Loop | `Proceed · Block · Retry · Approve · Rollback` ↺ | Test、Scanner、OPA、Signature、SLO 与 Approval 决定晋级、阻断和恢复 |

不应将箭头标成 `Learn / Train`。Harness 的隐私文档明确说明 Harness AI 的实时输入和相关上下文不持久化、不记录，也不用于模型训练；因此没有依据把运行结果画成自动模型训练闭环。[Core Capabilities - Data storage and privacy](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/)。

## 三、问题二：RBAC、Secrets、OPA、Scoped Token、Approval 应落在哪一层

### 3.1 放置矩阵

| 能力 | 图中位置 | 是否跨层 | 精确边界与官方证据 |
|---|---|---|---|
| RBAC | 左侧 `Identity & Access` Trust Rail | **是** | 以 User/Service Account/API Key 为 Principal，把 Role 与 Resource Group 绑定到 Account/Org/Project 资源和操作；约束 UI、API/MCP、Pipeline 与资源访问。[RBAC in Harness](https://developer.harness.io/docs/platform/role-based-access-control/rbac-in-harness/) |
| Secrets | 左侧 `Credential & Secret` Trust Rail | **是** | Connector、Pipeline Step、Agent/MCP Credential 可引用 Secret；Secret 在 Runtime 解析、注入和掩码，Delegate 可从客户 Secret Manager 取值。它是跨层凭据能力，不是一次性末端 Gate。[Secrets Overview](https://developer.harness.io/3k-docs/platform/getting-started/secrets/)、[Use Text Secrets](https://developer.harness.io/docs/platform/secrets/add-use-text-secrets/) |
| OPA | 右侧 `Policy` Trust Rail，并把 Enforcement Point 落入 Control Plane | **是，但在明确点执行** | Policy 可在 Save、Run、Step Start 或显式 Policy Step 处求值；Worker Definition/Use 也可被 Policy 治理。不要画成 Agent 自己判断是否合规。[Governance Overview](https://developer.harness.io/docs/platform/governance/policy-as-code/harness-governance-overview/)、[Worker Policy Governance](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) |
| Scoped Token | `Pipeline Control → Worker Runtime / Harness Tool` 边界上的凭据标签 | **否，不是通用全局 Rail** | Permission Block 位于 CI/STO/SCS/IaCM Stage 或 CD/Custom Containerized Step Group；Token 注入该范围内每个 Step。能力受 `HARNESS_TOKEN_INJECT` 控制，Trigger Run 当前不注入，且两份官方文档对 Principal 语义冲突。[Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)、[Agent Permissions](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/) |
| Approval | Pipeline Control Plane 内的显式 Gate | **否，入口可跨 UI/IDE/API，但语义属于 Pipeline** | Harness Approval Stage/Step 暂停 Pipeline，收集 User Group、Approver、Input、Comment 和 Timeout 决策后再继续或结束。[Harness Approval](https://developer.harness.io/docs/platform/approvals/adding-harness-approval-stages/) |
| Audit | 右侧 `Accountability` Trust Rail | **跨层汇总，但非单一 Gate** | Audit Trail 记录 Account/Org 范围的资源动作与 YAML Diff；Pipeline Start/End 和 Stage Start/End 默认不记录，需要显式开启 Execution Audit Events。[Audit Trail](https://developer.harness.io/docs/platform/governance/audit-trail/) |

### 3.2 一页图的最简表达

- 左轨：`Identity & Credentials — RBAC · Secrets`
- 右轨：`Policy & Accountability — OPA · Audit`
- Pipeline 内：`Approval · Failure Strategy · Rollback`
- Pipeline 到 Worker/Tool 的边界：`Scoped Token ⚠ Flag · Trigger Gap · Docs Conflict`

不建议把这五项一起塞进底部“安全层”。它们分别回答“谁可以做”“凭据如何获得”“何时允许继续”“运行时拿到什么权限”“事后如何追责”，应用点不同。

## 四、问题三：Observability / Audit / Accountability 由谁产生数据，如何汇总

### 4.1 数据来源不是一个统一的 Observability Plane

| 数据生产层 | 产生的数据 | 主要汇总位置 | 官方证据 |
|---|---|---|---|
| Interaction / Trigger | User/Service Account、Prompt/Request、Commit/Webhook/Schedule 等触发上下文 | Chat/入口上下文、Trigger 与 Pipeline Execution；DevOps Agent 的 Prompt 内容不能假设长期留存 | [Core Capabilities - Privacy](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/)、[Triggers](https://developer.harness.io/docs/platform/triggers/triggers-reference/) |
| Agent | Output Variable、Workspace File、建议、PR/Comment、动作结果；首发资料还声明 Token/Cost/Provenance | Pipeline Execution、下游 Step、Agent 执行视图；Token/Cost/Provenance 需以具体账户功能为准 | [Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)、[Worker Agents Launch](https://www.harness.io/blog/introducing-autonomous-worker-agents) |
| Context / Tool | MCP/API Request、Resource Read/Write/Execute、返回结果、授权主体 | MCP Client 日志、Harness Resource/Audit、目标系统日志；没有证据支持所有 MCP Prompt/Tool Trace 自动汇入同一个平台 Store | [Harness MCP Server](https://developer.harness.io/docs/platform/harness-ai/harness-mcp-server/) |
| Pipeline Control | Pipeline/Stage/Step 状态、Inputs/Outputs、Approval Comment、Failure/Retry/Rollback | Pipeline Execution View；部分 Lifecycle Event 可选写入 Audit Trail | [Pipeline Executions](https://developer.harness.io/docs/platform/pipelines/executions-and-logs/)、[Audit Trail](https://developer.harness.io/docs/platform/governance/audit-trail/) |
| Runtime / Delegate | Container/Step stdout-stderr、Plugin Log、Delegate Task/Connectivity Error | Step Log、Delegate Log、执行基础设施日志 | [Delegate Logs](https://developer.harness.io/docs/platform/delegates/delegate-concepts/delegate-logs/)、[Worker Troubleshooting](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) |
| Outcome Oracle / Module | Test Result、Scan Finding、Policy Decision、Evidence/Signature、Health/SLO、Incident Timeline、Experiment Result | 各模块结果页和 Evidence Store；Pipeline 只消费或展示其中一部分 | [AI Test Overview](https://developer.harness.io/docs/ai-test-automation/get-started/overview/)、[STO](https://developer.harness.io/docs/security-testing-orchestration/key-concepts/fail-pipelines-by-severity/)、[SCS Evidence](https://developer.harness.io/docs/software-supply-chain-assurance/evidence-store/overview/)、[SLO Rollback](https://developer.harness.io/docs/service-reliability-management/manage-slo/automated-deployment-rollback/) |

### 4.2 图中应画成五个汇总面

1. **Pipeline Execution**：运行中心视图，汇总 Stage/Step 状态、日志、输出、Approval、Failure 与 Retry/Rollback；
2. **Account / Org Audit Trail**：身份、资源变更和责任追踪；Execution Lifecycle Event 不是默认完整开启；
3. **Module Result Stores**：Test、STO/SCS、FME、AI SRE、SLO/Health 等领域事实源；
4. **Dashboard / Knowledge Graph / HQL**：对已建模的平台数据做跨模块 Read/Query/Analyze，而不是自动收纳所有 Prompt、Reasoning 与 Tool Trace；[Knowledge Graph/HQL](https://www.harness.io/blog/why-harness-ai-uses-knowledge-graph)、[HQL](https://developer.harness.io/docs/platform/dashboards/dashboard-standard/harness-query-language/)；
5. **AgentTrace / Evals（可选）**：官方开源能力可记录 Model/Tool/Retrieval Trace 并把 Eval 变成 CI Gate，但不能推断 Harness 所有产品 Agent 已默认接入。[AgentTrace](https://www.harness.io/blog/introducing-agent-trace)、[harness-evals](https://github.com/harness/harness-evals)。

### 4.3 Accountability 的最小链条

一条可追责记录至少需要：

`Subject / Service Account → Credential / Scoped Token → Agent & Version → Tool / Resource Action → Pipeline Execution → Oracle Outcome → Approval / Policy Decision`

当前必须显式标注的缺口：

- Trigger 发起的 Worker Run 当前没有 Scoped Token；
- Hosted MCP 使用 Harness User Identity 与 RBAC，OSS/self-hosted MCP 通常使用 PAT 或 Service Account Token，两者的主体归因不同；[Harness MCP Server](https://developer.harness.io/docs/platform/harness-ai/harness-mcp-server/)；
- Audit Trail 的 Pipeline Lifecycle Event 默认未开启；[Audit Trail](https://developer.harness.io/docs/platform/governance/audit-trail/)；
- Code/Security 等专项 Agent 可能使用 PAT、LLM API Key 或模块专用凭据，不能套用 Worker Scoped Token 结论。

## 五、问题四：入口在 2026-07-23 的可用状态与限制

| 入口 | 当前状态 | 开发者实际可用方式 | 关键限制与官方证据 |
|---|---|---|---|
| Harness UI | **GA 能力组合** | DevOps Agent 在 UI 中创建/修改 Pipeline 和资源、生成 OPA、分析失败；模块专项 Agent 也在各产品 UI 中出现 | Harness AI 必须启用，能力受 License/RBAC 约束；DevOps Agent 仅 Harness UI；SMP 不支持 Harness AI；不支持 BYOM。[AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/)、[DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/)、[Core Capabilities](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/) |
| VS Code Extension | **GA** | VS Code、Cursor、Windsurf、AntiGravity 中查看 Pipeline/Execution/Log，Approve/Reject、Rerun/Abort，并向 AI Assistant 注入失败上下文 | VS Code ≥1.85 或兼容 OpenVSX；用 PAT；它主要是执行监控/操作入口，不等同于 DevOps Agent 在 IDE 内运行。[VS Code Extension](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-ide/vscode-extension/)、[AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/) |
| Cursor Plugin | **已正式文档化并可安装；未见独立 GA/Beta 标签** | Cursor Marketplace 或官方仓库；内含 30 Skills、11 MCP Tools，通过 Hosted MCP OAuth 使用 Harness 能力 | Cursor ≥0.48；账户 OAuth 需联系 Support；Governance Hooks 另需 `HARNESS_API_KEY` 与 Account ID，缺少时 fail open。[Cursor Plugin](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-ide/cursor-plugin/) |
| Hosted MCP | **MCP Server GA** | SaaS 远程 MCP，OAuth 后以 Harness User Identity/RBAC 调用 Harness Resource/Action | 仅 SaaS；OAuth 需 Support 开启；License/Cluster 决定 Tool 可见性；Dedicated/非默认 Region 需 Support URL。[MCP Server](https://developer.harness.io/docs/platform/harness-ai/harness-mcp-server/)、[AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/) |
| OSS / self-hosted MCP | **官方开源实现；MCP 产品能力 GA** | npm、stdio/HTTP、Docker、Kubernetes；PAT 或 Service Account Token | OSS/self-hosted OAuth 仍未作为已支持路径；Write Elicitation 依赖 Client，也可关闭，不能当作强制人工 Gate。[MCP Server](https://developer.harness.io/docs/platform/harness-ai/harness-mcp-server/)、[harness/mcp-server](https://github.com/harness/mcp-server) |
| Harness Skills | **官方文档和开源目录；未见独立 GA/Beta 标签** | Markdown 工作流指令，由兼容 AI Assistant 调用 Harness MCP/API 完成任务 | Skills 不是 Agent Runtime；需要兼容 Client、MCP、Credential 和目标模块权限；可用范围取决于配套 Tool。[Harness Skills](https://developer.harness.io/docs/platform/harness-ai/harness-skills/)、[harness/harness-skills](https://github.com/harness/harness-skills) |
| Harness CLI 3.0 | **Public Beta** | 交互终端登录；脚本/CI 使用 API Key，提供统一 Harness 命令入口 | 支持 macOS/Linux amd64/arm64；Windows 使用 WSL；当前命令与模块覆盖仍应逐项核对，不能按 GA 入口表达。[Harness CLI Overview](https://developer.harness.io/docs/platform/harness-cli/harness-cli-overview)、[AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/) |

因此，图中可以共用一个 `Developer Entry Surfaces` 容器，但要把 `Harness UI`、`VS Code Extension`、`Cursor + Hosted MCP + Skills`、`OSS MCP Client`、`CLI 3.0 Beta` 分开标识；它们的身份、状态和行为边界不同。

## 六、问题五：三类 Agent 的职责、边界、输出、模型与 Runtime

### 6.1 组合矩阵

| Agent 类型 | 职责与入口 | 典型输出 | 模型与 Runtime | 不能外推的结论 |
|---|---|---|---|---|
| DevOps Agent | Harness UI 内创建/修改 Pipeline、Stage、Step、Service、Environment、Connector、Secret、OPA 和 GitOps 资源；分析执行失败 | YAML/Resource、OPA Rego、Pipeline Summary、Failure Analysis、Fix Recommendation、GitOps Query/Action | Harness Managed Claude Opus 4.6，经 AWS Bedrock/Google Vertex AI；不支持 BYOM；GA | 不能说它在任意 IDE、Pipeline Runtime 或客户 Kubernetes 中运行；Auto-repair 仍需用户 Review/Accept。[DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/)、[AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/) |
| Worker Agents | 由 Instructions、Model Connector、可选 MCP、Inputs/Env/Outputs 构成，以 Agent Step 进入 CI/CD/IaCM/STO/SCS/Custom | Output Variable、Workspace File、PR/Comment、Harness/MCP Action；输出可进入下游条件/步骤 | Harness Managed Anthropic/OpenAI，或客户 Anthropic/Bedrock/OpenAI Connector；Harness Cloud 仅 CI/STO/SCS/IaCM，或客户 Kubernetes + Delegate；CD/Custom 需 Containerized Step Group；GA | Delegate 是 Outbound Connectivity/Task Execution Bridge，不应等同 Agent Runtime；`max_turns` 不是统一金额预算；Scoped Token 也不是所有 Agent 的通用权限模型。[Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)、[Model Connectors](https://developer.harness.io/docs/platform/harness-ai/model-connector/) |
| 专项 Agents | 分布在 Code Quality、AI Test、Security、FME、AI SRE，以产品模块自己的入口和生命周期工作 | PR/Comment/Test、Test Result、Remediation/Fix PR、Metric/Experiment Summary、Timeline/Key Event/RCA Theory/Runbook Result | 模型、Credential、容器/Pod、模块服务和可用状态逐项不同 | 不能把它们都画成 Worker Agent，也不能假设共用 Model Connector、Scoped Token、Harness Cloud Runtime 或客户 Kubernetes Runtime |

### 6.2 专项 Agent 必须拆开的输出与 Runtime

| 专项能力 | 官方职责与输出 | Runtime / 模型 / 状态边界 | 官方证据 |
|---|---|---|---|
| Code Quality | Code Review → PR Comment；Coverage → Tests/Report；Autofix → Diagnosis/Fix Branch/PR | 当前文档示例使用普通 Run Step、专用 Container、Harness PAT、Anthropic Key、Git Connector；不是通用 Worker Definition | [Code Quality Agents](https://developer.harness.io/3k-docs/ai/code-agent/)、[Code Repository AI Agents](https://developer.harness.io/docs/code-repository/pull-requests/ai-agents/) |
| AI Test | 自然语言 Test、Agentic Execution、Self-healing；输出 Test Definition、Test Run、Pass/Fail 与 Failure Detail | Test 在隔离 Kubernetes Pod 运行；AI Test Copilot 在总览标 GA，但完整模块需开通；2026.06 新 Agentic Executor 仍受 Feature Flag 控制 | [AI Test Overview](https://developer.harness.io/docs/ai-test-automation/get-started/overview/)、[AI Test Release Notes](https://developer.harness.io/release-notes/ai-test-automation/)、[AI Test in CD](https://developer.harness.io/3k-docs/ai-test-automation/integrations/harness-cd/) |
| Security | STO AI Remediation 生成 Remediation/Code Suggestion；Zero Day/Manifest Remediator 可生成 Fix PR/YAML | Security Agent 示例使用专用 Run Step/Container/API Keys；STO/SCS AI Remediation 在总览标 GA，但具体 Repository、Scanner、License 条件仍适用 | [STO AI Remediation](https://developer.harness.io/docs/security-testing-orchestration/remediations/ai-based-remediations/)、[Security Agents](https://developer.harness.io/3k-docs/ai/security-agent/)、[AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/) |
| FME Release Agent | FME In-app Chatbot；回答产品/发布问题，并生成 Metric/Experiment Summary | 总览标 GA；管理员需启用；实验数据 Summary 使用 OpenAI Subprocessor；不能画成自动 Release Decision | [FME Release Agent](https://developer.harness.io/docs/feature-management-experimentation/release-agent/)、[AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/) |
| AI SRE | AI Scribe 产出 Timeline/Key Event/Action Item；RCA Change Agent 产出带置信度 Theory 并随新 Event 重算；Runbook 是独立确定性响应工作流 | AI Scribe 在总览标 GA，但产品页仍要求联系 Support；Investigator Agent Pipeline 为 EA，其他能力有 Feature Flag；不是 Worker Runtime | [AI Scribe](https://developer.harness.io/docs/ai-sre/ai-agent/)、[RCA Change Agent](https://developer.harness.io/3k-docs/ai-sre/ai-agent/rca-change-agent/)、[Runbooks](https://developer.harness.io/docs/ai-sre/runbooks/)、[AI SRE Support Matrix](https://developer.harness.io/docs/ai-sre/resources/whats-supported/) |

## 七、可直接用于一页图的内容模型

### 7.1 中央主链

```text
场景与应用
Code/PR · Build/Test · Security · Release/FME · Incident/SRE
        │ Intent / Event ↓                         ↑ Artifact / Outcome / Evidence
        │
开发者入口
Harness UI · VS Code · Cursor + Hosted MCP + Skills · OSS MCP Client · CLI 3.0 Beta
        │
Agent Portfolio
DevOps Agent · Worker Agents · Code / Test / Security / FME / AI SRE
        │
Context & Tool Plane
Knowledge Graph + HQL（已建模查询） │ Harness / Third-party MCP（工具与动作）
        │
Deterministic Control Plane
Pipeline DAG · Sequential/Parallel · Approval · Failure Strategy · Rollback · Max Turns
        │
Execution Plane
Harness Cloud Runtime │ Customer Kubernetes Runtime
          Harness SaaS ↔ Delegate ↔ Customer Network
        │
Outcome Oracles
Test · Scanner · OPA · Signature/Attestation · SLO/Health · Human Review
        └──────────────── Proceed / Block / Retry / Rollback ────────────────↺
```

### 7.2 两侧 Trust Rails

```text
左：Identity & Credentials                  右：Policy & Accountability
RBAC · Secrets                              OPA · Approval Event · Audit
             Scoped Token 只画在 Pipeline → Worker/Tool 边界
```

### 7.3 页面主张

**Harness 不是用 Agent 取代 Pipeline，而是把多形态 Agent 接入同一软件交付闭环：Agent 产生候选判断和动作，Pipeline 编排并限制执行，Test / Scan / Policy / Signature / SLO / Human Review 决定是否正确和能否晋级。**

这句话是基于官方产品机制形成的分析结论，不是 Harness 官方原句。它同时保留了三个关键边界：

1. DevOps Agent、Worker Agents 和专项 Agent 不是同一 Runtime；
2. Knowledge Graph/HQL 与 MCP 是互补能力，不是所有 Agent 都遵循的强制串行调用顺序；
3. 结果回路是业务/控制反馈，不是模型自动训练回路。

## 八、图中必须保留的脚注

1. `Architecture is an analytical view synthesized from Harness official product documentation; it is not an official Harness reference architecture.`
2. `Product state as of 2026-07-23: MCP Server, VS Code Extension, DevOps Agent and Harness Agents are GA; CLI 3.0 is Public Beta; some account capabilities still require Support, Feature Flags or module licenses.`
3. `Scoped Token: HARNESS_TOKEN_INJECT feature flag; trigger-initiated runs currently have no scoped token; current official documents conflict on principal semantics.`
4. `Audit Trail is not a complete default execution trace; pipeline execution audit events require explicit account enablement.`
5. `The feedback loop denotes operational evidence and control decisions, not automatic model training.`
