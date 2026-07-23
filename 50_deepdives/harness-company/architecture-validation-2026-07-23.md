---
title: Harness 2026 产品组合与 Agentic Software Delivery 架构核验
aliases:
  - Harness Architecture Validation 2026-07-23
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
scope: harness-2026-product-architecture
source_policy: official-primary-only
confidence: medium-high
presentation_ready: true
---

# Harness 2026 产品组合与 Agentic Software Delivery 架构核验

> [!abstract] 核验结论
> 用户给出的说明**总体方向准确，足以作为一页公司级 Presentation 的事实底座**：Harness 已经不是单一的“Pipeline 内 Agent”，而是入口、Agent、结构化上下文、Tool、Pipeline/Runtime，以及治理与证明机制的组合；DevOps Agent、Worker Agents 和专项 Agent 也确实不能共享同一 Runtime、模型与权限假设。
>
> 但“六层”是本专题基于官方产品组合做出的**分析架构**，不是 Harness 官方发布的六层参考架构。正式汇报必须增加五个限定：一，Harness CLI 3.0 是 **Public Beta**，IDE、MCP、Skills 与 CLI 还是相互嵌套的入口能力，不是五套并列 Agent；二，Knowledge Graph/HQL 是 Harness 公布的结构化 Read/Query/Analyze 路径，但没有产品文档证明每一个自定义 Worker Agent 都会自动获得完整 Knowledge Graph；三，FME Release Agent 主要输出知识回答和实验结果摘要，不应与 PR、Test Result、Remediation 或 RCA Theory 混成同一输出类型；四，Pipeline 的“预算”目前应具体写成 `max_turns`、并行约束和成本可观测，而不是未证实的统一金额预算器；五，Scoped Token 的 Principal 语义在两份当前官方文档中冲突，且 Trigger Run 当前不注入 scoped token。

## 一、范围与证据口径

本次核验逐条检查以下陈述：

1. Harness 2026 产品组合是否可以分成六层；
2. DevOps Agent、Worker Agents、专项 Agent 的职责、模型与 Runtime 是否准确；
3. Software Delivery Knowledge Graph/HQL 与 MCP 的架构分工；
4. Pipeline、Delegate、Harness Cloud、客户 Kubernetes 的关系；
5. RBAC、OPA、Approval、Scoped Token、Audit、Test、Scan、Signature、SLO 能否构成治理与证明层；
6. “Pipeline 没有被 Agent 取代，而成为概率决策的确定性控制结构”是否有证据支撑。

仅使用 Harness Developer Hub、Harness 官方工程文章、官方产品/发布材料和 Harness 官方 GitHub 仓库。证据等级如下：

| 标记 | 证据类型 | 本文件中的使用方式 |
|---|---|---|
| A | 当前官方产品文档、发布状态表、官方仓库 | 支持能力、配置、限制、产品状态和公开实现存在性 |
| B | Harness 官方工程文章或发布博客 | 支持架构意图和厂商实现说明，不等于任意账户已经完成生产验收 |
| Gap | 官方文档冲突、缺少公开实现或状态 | 不可写成普遍事实，需账户、Cluster、Trigger 或实验验证 |

## 二、逐条 Claim Check

| ID | 用户陈述 | 核验结论 | 精确边界与一手证据 |
|---|---|---|---|
| ARCH-V01 | Harness 2026 产品组合应分成六层 | **方向正确，但六层是分析框架** | 官方资料分别提供 UI/IDE/Pipeline 三个工作面、MCP/Skills/CLI、Knowledge Graph、Worker Runtime 和治理能力，没有发布名为“六层架构”的官方分类。可在 Presentation 标注为“基于 Harness 官方产品组合的分析视图”。[Harness AI Core Capabilities](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/)（A）；[Harness AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/)（A） |
| ARCH-V02 | Harness UI、IDE、MCP、CLI、Skills 都是入口 | **正确，需标状态并避免重复计数** | Harness 官方将 UI、IDE、Pipeline 定义为三个工作面；MCP Server 为外部 AI Client 提供 Tool，Skills 是调用 MCP 的指令包，IDE 插件也常通过 MCP 工作；Harness CLI 3.0 为 Public Beta。故图上应将它们画成“交互入口/客户端”，而不是五个平级执行引擎。[Core Capabilities](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/)（A）；[MCP Server](https://developer.harness.io/docs/platform/harness-ai/harness-mcp-server/)（A）；[Harness Skills](https://developer.harness.io/docs/platform/harness-ai/harness-skills/)（A）；[AI Overview 状态表](https://developer.harness.io/docs/platform/harness-ai/overview/)（A） |
| ARCH-V03 | DevOps Agent 在 Harness UI 创建/修改 Pipeline、Service、Environment、Connector、Secret、OPA 和 GitOps 资源 | **正确** | 当前文档明确支持 Pipeline/Stage/Step、多模块 Pipeline、OPA Rego、Service、Environment、Connector、Secret 以及 13 类 GitOps 资源和操作；只在 Harness UI 中使用。[DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/)（A） |
| ARCH-V04 | DevOps Agent 使用 Harness Managed Model，不支持 BYOM | **正确** | 当前文档指定 Claude Opus 4.6，经 AWS Bedrock 与 Google Vertex AI 托管；FAQ 明确 AI Chat 与 DevOps Agent 不支持 Bring Your Own Model。[DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/)（A）；[Core Capabilities FAQ](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/)（A） |
| ARCH-V05 | Worker Agent 由 Instructions、Model Connector、可选 MCP 构成，并作为 Agent Step 进入 CI、CD、IaCM、STO、SCS、Custom Stage | **正确** | Worker Agent Definition 还可包含 Inputs、Environment Variables 和 Outputs；Pipeline 中的 Agent Step 只引用 Agent 名称/版本。CD 与 Custom 必须放进 Containerized Step Group。[Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)（A） |
| ARCH-V06 | Worker Agent 支持 Harness Managed 或客户 Anthropic/OpenAI Connector | **正确** | 支持 Harness 自动提供的 `harnessAnthropic` / `harnessOpenAI` Managed Connector，也支持 Direct Anthropic、AWS Bedrock 和 OpenAI Model Connector。注意 Managed Connector 需要 LLM Gateway 权限，声明显式 Permission Block 后还需保留 `ai_llm_gateway: access`。[Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)（A） |
| ARCH-V07 | Worker Agent 可运行于 Harness Cloud 或客户 Kubernetes | **正确，需画出 Stage 限制** | Harness Cloud Runtime 仅明确支持 CI、STO、SCS、IaCM；客户侧通过 Harness Delegate 在 Kubernetes 运行。CD/Custom 依赖 Containerized Step Group，不能画成任意 Stage 都直接使用相同 Cloud Runtime。[Worker Agents - Infrastructure and execution](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)（A） |
| ARCH-V08 | 专项 Agent 分布在 Code Quality、AI Test、Security、FME、AI SRE | **正确，但不是一个统一产品族或 Runtime** | 官方文档确有 Code Review/Coverage/Autofix、AI Test Copilot/Agentic Executor、Security Agents、FME Release Agent、AI Scribe/RCA Change Agent。Code Quality 与 Security 示例还是普通 Run Step、专用镜像、PAT/LLM Key；AI Test 使用自己的 Kubernetes Pod/执行器；FME 与 AI SRE 为各自模块内 Agent。不能套用 Worker 的容器、Scoped Token 或 BYOM 结论。[Code Quality Agents](https://developer.harness.io/3k-docs/ai/code-agent/)（A）；[Security Agents](https://developer.harness.io/3k-docs/ai/security-agent/)（A）；[AI Test Automation](https://developer.harness.io/docs/ai-test-automation/get-started/overview/)（A）；[FME Release Agent](https://developer.harness.io/docs/feature-management-experimentation/release-agent/)（A）；[AI Scribe](https://developer.harness.io/docs/ai-sre/ai-agent/)（A） |
| ARCH-V09 | 专项 Agent 分别产生 PR、Test Result、Remediation、RCA Theory 或 Runbook Input | **部分正确，需要改写输出映射** | Code Quality 产生 PR Comment、测试/Report 和修复 PR；AI Test 模块产生 Test/Test Run/Pass-Fail Result；Security 产生 Remediation、代码建议、修复 PR 或 YAML；AI SRE 的 RCA Change Agent 产生带置信度的 Theory，Scribe 产生 Timeline/Key Event/Action Item。FME Release Agent 的主要输出是知识回答、Metric/Experiment Summary。Runbook 是独立的确定性自动化工作流，可消费 Incident/Trigger 数据，不是官方定义的统一“Agent Output”。[Code Quality Agents](https://developer.harness.io/3k-docs/ai/code-agent/)（A）；[AI Test Pipeline](https://developer.harness.io/3k-docs/ai-test-automation/integrations/harness-cd/)（A）；[AI Remediation](https://developer.harness.io/docs/security-testing-orchestration/remediations/ai-based-remediations/)（A）；[RCA Change Agent](https://developer.harness.io/3k-docs/ai-sre/ai-agent/rca-change-agent/)（A）；[Runbooks](https://developer.harness.io/docs/ai-sre/runbooks/)（A） |
| ARCH-V10 | Knowledge Graph/HQL 优先处理已建模的跨模块 Read/Query/Analyze | **正确，证据来自官方工程架构文** | Harness 明确提出 Tier 1 默认使用 Knowledge Graph + HQL 做 Read/Query/Analyze，声明跨模块关系、字段、聚合和 Row-level Authorization；日志/Trace 通过 HQL Event Envelope 再做 Scoped Retrieval。[Knowledge Graph/HQL](https://www.harness.io/blog/why-harness-ai-uses-knowledge-graph)（B） |
| ARCH-V11 | Knowledge Graph 是所有 Worker 的自动共享上下文 | **未证实，不应这样画** | Worker 产品文档的通用上下文入口是 Instructions、Inputs、Expressions、Workspace/File、Environment Variable 和显式 MCP Connector；首发材料把 Knowledge Graph 称为 Managed Agents 的组织记忆，但没有文档化“任意 Custom Worker 自动装载全部 Knowledge Graph”。图中应使用虚线/“按产品与连接可用”，而非一条无条件总线。[Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)（A）；[Worker Agents 首发](https://www.harness.io/blog/introducing-autonomous-worker-agents)（B） |
| ARCH-V12 | MCP 处理长尾系统和 Create/Update/Execute | **正确，需区分两类 MCP 路径** | Harness MCP Server 提供 11 个通用 Verb，含 List/Get/Search/Describe/Create/Update/Delete/Execute；Knowledge Graph 工程文把外部 MCP 定义为未建模数据与长尾扩展。Worker Agent 的 MCP Connector 还必须显式配置 URL 与 Credential。外部 IDE 使用的 Hosted/Open-source MCP 与 Pipeline 内 Worker 绑定的 MCP Connector 不应画成同一个无边界总线。[Harness MCP Server](https://developer.harness.io/docs/platform/harness-ai/harness-mcp-server/)（A）；[Worker MCP Connector](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)（A）；[Knowledge Graph/HQL](https://www.harness.io/blog/why-harness-ai-uses-knowledge-graph)（B） |
| ARCH-V13 | Pipeline 负责顺序、并行、预算、Approval、Failure Strategy、Rollback | **大体正确；“预算”需具体化** | Pipeline 原生支持顺序/并行、条件、Step Group、Approval、Failure Strategy 和 Rollback。Worker 使用 `max_turns` 限制推理步数并管理成本/延迟；官方首发文称 Token/Cost 可按 Execution/Agent/Pipeline 查看。尚未找到产品文档支持“Pipeline 提供统一金额预算门禁”，故图中宜写“轮次/并发/成本约束”，不写泛化的“预算控制器”。[Step Groups](https://developer.harness.io/docs/platform/pipelines/use-step-groups/)（A）；[Failure Strategies](https://developer.harness.io/docs/platform/pipelines/failure-handling/define-a-failure-strategy-on-stages-and-steps/)（A）；[Approval](https://developer.harness.io/docs/platform/approvals/adding-harness-approval-stages/)（A）；[Worker `max_turns`](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)（A）；[Worker 首发](https://www.harness.io/blog/introducing-autonomous-worker-agents)（B） |
| ARCH-V14 | RBAC、OPA、Approval、Scoped Token、Audit 构成治理层 | **正确，但 Scoped Token 与 Audit 需要警示** | RBAC 决定主体资源权限；OPA 可在保存/运行/Step 前后阻断；Approval 产生人工 Gate；Worker Scoped Token 定义细粒度 Runtime 权限；Audit Trail 记录资源动作。Scoped Token 在 `HARNESS_TOKEN_INJECT` Feature Flag 后，Trigger Run 当前不注入，而且 7/15 与 7/20 文档的 Principal 语义冲突。Pipeline Execution Audit Event 也不是默认开启。[RBAC](https://developer.harness.io/docs/platform/role-based-access-control/rbac-in-harness/)（A）；[OPA](https://developer.harness.io/docs/platform/governance/policy-as-code/harness-governance-overview/)（A）；[Approval](https://developer.harness.io/docs/platform/approvals/adding-harness-approval-stages/)（A）；[Worker Permissions](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)（A）；[Agent Permissions](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/)（A）；[Audit Trail](https://developer.harness.io/docs/platform/governance/audit-trail/)（A） |
| ARCH-V15 | Test、Scanner、Policy、Signature、SLO 定义结果是否正确 | **作为“外部 Oracle/证明层”基本正确，但它们不是同一种证明** | Test 证明定义过的行为；Scanner/Policy 判断漏洞与合规阈值；Signature/Attestation 证明来源与供应链属性；Continuous Verification/SLO 观察运行健康与 Error Budget；人工 Approval 接受剩余业务风险。它们都比 Agent 自评更适合作为晋级条件，但没有任一单独证明“业务整体正确”。[STO Failure Criteria](https://developer.harness.io/docs/security-testing-orchestration/key-concepts/fail-pipelines-by-severity/)（A）；[SLO Rollback](https://developer.harness.io/docs/service-reliability-management/manage-slo/automated-deployment-rollback/)（A）；[Continuous Verification](https://developer.harness.io/3k-docs/continuous-delivery/verify/verify-deployments-with-the-verify-step/)（A）；[Artifact Signature](https://developer.harness.io/docs/software-supply-chain-assurance/artifact-security/sign-verify/sign-artifacts/)（A） |
| ARCH-V16 | Pipeline 没有被 Agent 取代，而成为概率决策的确定性控制结构 | **分析判断成立，置信度高** | Worker 被产品化为 Pipeline Step，继续继承条件、并行、Approval、Failure Strategy、Rollback 和外部 Scan/Verify；Agent Output 也可作为下游条件输入。因此更准确的系统表述是“Agent 生成候选判断或动作，Pipeline 与外部 Oracle 决定是否晋级”。“概率决策的确定性控制结构”是本专题归纳，不是 Harness 官方术语。[Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)（A）；[Worker 首发](https://www.harness.io/blog/introducing-autonomous-worker-agents)（B） |

## 三、六层产品组合：可以画，但要这样定义

| 分析层 | 图中建议名称 | 可以放入的官方能力 | 不应暗示的事实 |
|---|---|---|---|
| 1 | **Experience / Entry** | Harness UI、VS Code、Cursor、Claude/Codex/Windsurf 等 MCP Client、Harness Skills、Harness CLI 3.0、Gemini CLI Extension | 不是所有入口都运行同一个 Agent；Skills 和 IDE 往往通过 MCP 调用 Harness；CLI 3.0 为 Public Beta |
| 2 | **Agent Portfolio** | DevOps Agent、Worker Agents、Code Quality、Security、AI Test、Release Agent、AI Scribe、RCA Change Agent | 不共享统一模型、容器、权限、凭据或部署状态 |
| 3 | **Context & Knowledge** | Software Delivery Knowledge Graph、HQL、Pipeline Inputs/Outputs、Execution/Trigger Context、Logs/Traces 的 Scoped Retrieval | Custom Worker 不自动拥有完整 Knowledge Graph；Context 可用性依赖 Agent 类型、显式 Connector 和账户能力 |
| 4 | **Tools & Actions** | Harness Hosted/Open-source MCP、第三方 MCP、Harness Connectors、平台 API、通用 `list/get/search/describe/create/update/delete/execute` | Tool 可见不等于获得授权；GitHub Connector Catalog 中用于 AI Chat 的 MCP Connector 当前不能直接用于 Worker |
| 5 | **Orchestration & Runtime** | Harness Pipeline；Agent Step；Step Group；Harness Cloud Runtime；Delegate；客户 Kubernetes；Container/Isolated VM | Delegate 是连接与任务执行桥梁，不应等同于 Agent 本身；Cloud Runtime 的 Stage 支持范围有限 |
| 6 | **Governance & Evidence** | RBAC、OPA、Approval、Scoped Token、Secret/Connector、Audit；Test、STO/Scanner、SCS Evidence/Signature、Continuous Verification、SLO、人工 Review | Audit 不是 Gate；OPA/Approval 不是结果测试；Scoped Token 尚有 Flag、Trigger 和文档冲突；任何单一 Oracle 都不证明整体正确 |

六层适合 Presentation 的原因不是“组件多”，而是能显示两类不同的闭环：

- **概率路径：** 人或事件 → Agent → Context/Tool → 建议、变更或动作；
- **确定性路径：** Pipeline → Test/Scan/Policy/Approval/Verify/SLO → 继续、阻断、重试或回滚。

Presentation 中应让第二条路径包住第一条，而不是把两条路径画成同权的水平流程。

## 四、三类 Agent 的准确职责与输出

### 4.1 DevOps Agent：Harness UI 中的设计与运维控制面

| 维度 | 核验结果 |
|---|---|
| 入口 | 仅 Harness UI |
| 主要任务 | 创建/修改 Pipeline、Stage、Step；创建/更新 Service、Environment、Connector、Secret；生成 OPA；分析失败；管理 GitOps Resource/Action |
| 模型 | Harness Managed Claude Opus 4.6，经 AWS Bedrock / Google Vertex AI；OpenAI 只作为官方文档中的受控 Failover 路径 |
| BYOM | 不支持 |
| 典型输出 | Pipeline/Resource YAML、OPA Rego、Failure Analysis、Fix Recommendation、GitOps Query/Action |
| 控制边界 | Pipeline YAML Auto-repair 需要用户检查 before/after 并 `Accept`；动作受模块许可与 RBAC 限制 |
| 状态 | Harness AI Overview 标为 GA；账户仍需启用 Harness AI，模块能力取决于 License |

证据：[DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/)、[Core Capabilities](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/)、[Harness AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/)。

### 4.2 Worker Agents：Pipeline 原生的通用 Agent Step

| 维度 | 核验结果 |
|---|---|
| 定义 | Instructions + Model Connector + 可选 MCP Connectors + Inputs/Environment/Outputs |
| Pipeline 支持 | CI、CD、IaCM、STO、SCS、Custom；CD/Custom 必须在 Containerized Step Group |
| 模型 | Harness Managed Anthropic/OpenAI，或客户 Direct Anthropic、AWS Bedrock、OpenAI Connector |
| Runtime | Harness Cloud（CI/STO/SCS/IaCM）或客户 Kubernetes + Delegate；容器运行于隔离 VM |
| 典型输出 | 结构化 Output Variable、Workspace File、PR/Comment、调用 Harness/MCP Action；输出可供下游 Step 作为条件或输入 |
| 成本边界 | `max_turns` 限制单次推理步骤；官方首发文声称显示 Token/Cost，但不是统一金额预算 Gate |
| 状态 | Harness Agents 在总览标为 GA；入口可能需 Admin/Support；Scoped Permission 仍需 `HARNESS_TOKEN_INJECT` Flag |

证据：[Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)、[Worker Agents GA](https://www.harness.io/press-and-news/harness-launches-autonomous-worker-agents-for-software-delivery)。

### 4.3 专项 Agent：领域职责成立，Runtime 必须逐项画

| 专项能力 | 当前官方职责 | 更准确的输出 | Runtime / 状态边界 |
|---|---|---|---|
| Code Quality | Code Review、Code Coverage、Autofix | PR Comment、Tests、`COVERAGE.md`、Fix Branch/PR | 当前文档示例为普通 Run Step + 专用镜像 + Harness/Anthropic/Git Credential；不能自动继承 Worker Scoped Token/Isolation 结论 |
| Security | Zero Day Remediation、Manifest Remediator、STO AI Remediation | Fix PR、Code Suggestion、Remediation Guidance、`manifest_remediation_results.yaml` | Security Agent 示例为专用 Run Step/镜像/Key；STO/SCS Remediation 在 AI Overview 标 GA，但具体 Agent Template 的统一 GA/Runtime 未单独证明 |
| AI Test Automation | 自然语言测试生成、Agentic Execution、Self-healing、Pipeline Test Step | Test Definition、Test Run、Pass/Fail、Failure Triage | 测试运行于独立 Kubernetes Pod；AI Test Copilot 在总览标 GA，但账户需销售开通；2026.06 新 Agentic Executor 仍受 Feature Flag 控制 |
| FME Release Agent | FME 内问答、文档帮助、Metric/Experiment Summary | Answer、Metric Summary、Experiment Summary | In-app Chatbot；总览标 GA；实验数据处理需管理员启用，Summary 使用 OpenAI Subprocessor |
| AI SRE | Scribe 捕获沟通和 Key Event；RCA Change Agent 关联变化并更新 Theory；Runbook 负责自动响应 | Timeline、Key Event、Action Item、RCA Theory、Runbook Execution Result | AI Scribe 在总览标 GA，但产品页仍要求联系 Support；Investigator Agent Pipeline 为 EA，Postmortem/Fire Drill 等有 Flag；Runbook 不是 Worker Runtime |

证据：[Code Quality Agents](https://developer.harness.io/3k-docs/ai/code-agent/)、[Security Agents](https://developer.harness.io/3k-docs/ai/security-agent/)、[AI Test Overview](https://developer.harness.io/docs/ai-test-automation/get-started/overview/)、[AI Test Release Notes](https://developer.harness.io/release-notes/ai-test-automation/)、[Release Agent](https://developer.harness.io/docs/feature-management-experimentation/release-agent/)、[AI Scribe](https://developer.harness.io/docs/ai-sre/ai-agent/)、[RCA Change Agent](https://developer.harness.io/3k-docs/ai-sre/ai-agent/rca-change-agent/)、[AI SRE Support Matrix](https://developer.harness.io/docs/ai-sre/resources/whats-supported/)。

## 五、Knowledge Graph、MCP、Pipeline 和 Oracle 的准确分工

### 5.1 Knowledge Graph/HQL：优先回答“已建模世界里发生了什么”

Harness 官方工程文给出的分层很清楚：

1. 已拥有且理解的数据，用 Knowledge Graph + HQL；
2. Logs/Traces 等大体量内容，先用 HQL 查建模 Event Envelope，再按 Key 做 Scoped Retrieval；
3. 已理解但不拥有的数据，用 Harness-managed Integration；
4. 未拥有、未建模的长尾系统，使用 External MCP。

因此“Knowledge Graph 优先做 Read/Query/Analyze”是准确的。但它首先是 Harness 的数据与 Agent 架构原则，不应画成所有 Agent 都获得同样 Context 的产品保证。官方工程文明确把 HQL 用于 Knowledge Graph 查询；当前产品文档也将 HQL 作为 Harness Data Platform/Dashboard 的查询语言公开，但没有公开一个“所有 Worker 直接调用 HQL”的统一接口。Presentation 应把 `Knowledge Graph + HQL` 画成 Context Plane，而不是每个 Agent 默认拥有的 Tool。[Knowledge Graph/HQL 架构](https://www.harness.io/blog/why-harness-ai-uses-knowledge-graph)（B）；[HQL 产品文档](https://developer.harness.io/docs/platform/dashboards/dashboard-standard/harness-query-language/)（A）。

### 5.2 MCP：回答“需要调用什么系统、做什么动作”

Harness MCP Server 已公开 11 个通用 Tool Verb 和 Resource Registry；写操作包括 Create、Update、Delete、Execute。Hosted MCP 可用用户 Harness Identity/RBAC，开源或自托管 MCP 可使用 PAT/Service Account Token；Worker 内的 MCP Connector 则需要显式 URL 和 Secret。

需要保留三个限制：

- MCP Tool 被加载不等于 Principal 有权限；
- Worker 文档明确，Connector Catalog 中的 GitHub MCP Connector 当前面向 AI Chat，不能直接用于 Worker，Worker 需 Hosted MCP 或 Custom Endpoint；
- 开源 MCP 的 Write Elicitation 依赖 Client 支持，也可通过 `HARNESS_SKIP_ELICITATION=true` 关闭，不能把“写操作必有人确认”画成系统不变量。

证据：[Harness MCP Server](https://developer.harness.io/docs/platform/harness-ai/harness-mcp-server/)、[Worker MCP Connector](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)、[官方源码](https://github.com/harness/mcp-server)。

### 5.3 Pipeline：决定动作的时序、组合和失败处理

Pipeline 继续提供：

- Sequential、Parallel、Conditional 和 Matrix 编排；
- Inputs/Outputs 与 Workspace Artifact Handoff；
- Approval、Manual Intervention、Retry、Abort、Failure Strategy 和 Rollback；
- Worker `max_turns`、并行上限与成本可见性；
- 在 Agent 前后插入 Build、Test、Scan、Policy、Verify、SLO 和人工 Gate。

这支持专题的核心判断：**Agent 没有替代 Pipeline；Agent 被封装成一个会产生概率输出和可变动作的 Step，Pipeline 则把它接入确定性的晋级/阻断/恢复结构。**

### 5.4 Oracle：决定“结果是否足以晋级”，而不是继续向 Agent 询问

| Oracle / Gate | 能证明什么 | 不能单独证明什么 |
|---|---|---|
| Build / Test | 可编译、已定义测试与契约通过 | 未覆盖需求、未知风险、生产行为 |
| Scanner / STO | 已配置 Scanner 与 Severity/Policy Criteria 通过 | 无漏洞、无误报、业务正确 |
| OPA Policy | 配置、资源或输出满足显式 Rego Rule | 运行效果、性能、用户价值 |
| Signature / Attestation / Evidence | Artifact 来源、签名、SBOM、构建证据满足要求 | Artifact 在生产运行正确 |
| Continuous Verification / SLO | 健康源、异常、SLI/Error Budget 满足阈值 | 未监控行为、长期业务目标 |
| Human Approval / PR Review | 有责任主体接受变更和剩余风险 | 审批人判断必然正确 |

## 六、产品状态与可获得性

| 能力 | 官方公开状态 | 账户/实现限定 |
|---|---|---|
| Harness Agents / Worker Agents | GA | AI Agents 入口可能需 Admin/Support；Scoped Token 需 Flag；Trigger Run 无 Scoped Token |
| DevOps Agent / Error Analyzer / Policy Generation / Resource Creation | GA | Harness UI only；Harness Managed Model；模块能力取决于 License；SMP 不支持 Harness AI |
| MCP Server | GA | Hosted MCP 需 OAuth 与 SaaS；Open-source/self-hosted 使用 API Key；资源数与 Toolset 仍快速变化 |
| Harness CLI 3.0 | Public Beta | 不应在图中使用 GA 样式 |
| VS Code Extension | GA | IDE 入口不代表 DevOps Agent 本身可在 IDE 运行；部分能力通过 MCP/Extension 实现 |
| FME Release Agent | GA | 管理员需启用 Release Agent 与实验数据处理 |
| AI Test Copilot | GA | 完整模块仍需销售开通；最新 Agentic Executor 受 Feature Flag 控制 |
| AI Scribe Agent | GA（总览） | 产品页仍要求 Support 开启；Investigator Agent Pipelines 为 EA，部分附属能力在 Feature Flag 后 |
| STO/SCS AI Remediation | GA（总览） | 具体 Scanner、Repository、License、EULA 与 AI Enablement 条件仍适用 |

来源：[Harness AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/)、[Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)、[AI Test](https://developer.harness.io/docs/ai-test-automation/get-started/overview/)、[AI SRE Support Matrix](https://developer.harness.io/docs/ai-sre/resources/whats-supported/)。

## 七、冲突、过时和未证实之处

### 7.1 Scoped Token 的 Principal 语义存在阻断性冲突

- [Worker Agents 7/20](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)：有效权限是 `declared grant ∩ invoking principal RBAC`；
- [Agent Permissions 7/15](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/)：Runtime Token 独立于 Pipeline Author 权限，并取代通过 MCP Connector 继承 Author Credential 的默认行为。

两者不能同时作为同一版本的精确语义。Presentation 可以写“支持细粒度 Scoped Token”，但不能写“所有 Agent 都严格继承触发人最小权限”。生产验收必须按 Manual、API、Service Account 和 Event Trigger 分别验证 Token Subject、Scope、Expiry 与 Audit Principal。

### 7.2 Trigger 能运行 Worker，但事件触发目前没有 Scoped Token

Worker 支持 Webhook、Artifact、Manifest、Schedule 等标准 Trigger；但同页 Limitations 又明确 Trigger-initiated Run 当前不注入 Scoped Token，声明权限无法对 Invoking Principal 求交集，支持仍在 Roadmap。图中 Trigger 箭头必须经过一个“Identity Gap / Account Validation”警示，而不是直接接入 Scoped Token。[Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)。

### 7.3 “No Ambient Permission”与默认只读权限存在措辞张力

Worker 安全段写“No ambient permissions”，但当前同页又列出在没有 `permissions` Block 时自动注入的 Core、Artifact Registry、Code Repository、Harness AI、CCM、FME、IaCM、IRO、STO 默认只读权限。准确说法应是：**没有未声明的写权限，但可能存在文档化默认读取权限。**

### 7.4 Audit Trail 不是默认完整记录所有 Pipeline Execution Event

官方 Worker 首发文称每次执行都有完整 Provenance；当前 Audit Trail 产品文档则明确 Pipeline Start/End、Stage Start/End 默认不记录，需要账户级开启 `Enable Pipeline Execution Audit Events`。因此图中 Audit 可写“可审计”，不能写“默认完整审计”。[Audit Trail](https://developer.harness.io/docs/platform/governance/audit-trail/)。

### 7.5 Knowledge Graph 不应画成所有 Agent 的隐式 Memory Bus

Harness 首发文章把 Knowledge Graph 称为 Agent 的组织记忆；但通用 Worker 文档要求用 Inputs、Expressions 和显式 MCP Connector 提供上下文。除非目标账户和具体 Agent 已经验证，图中应该画成“结构化 Context Service”，用实线连官方明确使用它的 Harness Managed AI，用虚线连 Custom Worker/专项 Agent。

### 7.6 “预算”尚无统一产品语义

公开资料支持 `max_turns`、Token/Cost 展示、Pipeline Parallelism Limit 和 Failure Strategy，但没有公开一个跨 Agent 的统一金额预算策略或超额硬阻断器。Presentation 应使用“轮次、并发与成本约束”，避免只写“预算”。

## 八、建议采用的修订说明

> Harness 2026 的 Agentic Software Delivery 组合可以用六层分析视图呈现：UI、IDE、MCP Client、Skills 与 Public Beta CLI 构成交互入口；DevOps Agent、Pipeline-native Worker Agents 与模块专项 Agent 构成执行主体；Knowledge Graph/HQL 为已建模的跨模块 Read/Query/Analyze 提供结构化上下文，Pipeline Inputs 和显式 MCP 补充运行上下文；Harness Hosted/Open-source MCP、第三方 MCP 与 Connector/API 承担长尾查询和 Create/Update/Execute；Harness Pipeline 负责顺序、并行、条件、轮次/成本约束、Approval、Failure Strategy 和 Rollback，Worker Runtime 运行在 Harness Cloud 或客户 Kubernetes + Delegate；RBAC、OPA、Scoped Token、Audit 与 Test/Scan/Signature/Continuous Verification/SLO/人工 Review 共同构成治理与证明边界。
>
> 三类 Agent 的职责和实现不可混用。DevOps Agent 仅在 Harness UI 中创建和管理 Pipeline、Resource、OPA 与 GitOps 对象，使用 Harness Managed Model，不支持 BYOM。Worker Agent 由 Instructions、Model Connector、可选 MCP、Inputs/Outputs 构成，以 Agent Step 进入 CI、CD、IaCM、STO、SCS 和 Custom Stage，支持 Harness Managed 或客户 Anthropic/OpenAI Connector，并按 Stage 条件运行在 Harness Cloud 或客户 Kubernetes。Code Quality、Security、AI Test、FME 和 AI SRE 的专项 Agent 分别产生 PR/Comment/Test、Test Run Result、Remediation、Experiment Summary、Timeline/RCA Theory 等领域输出；其 Runtime、Credential 和产品状态必须逐项标注。
>
> Harness 的关键架构分工是：Knowledge Graph/HQL 优先理解已建模数据，MCP 连接长尾系统并执行动作，Pipeline 把 Agent 与确定性 Step、Gate 和恢复策略编排在一起，外部 Test、Scanner、Policy、Signature、Continuous Verification、SLO 与人类 Review 决定结果是否足以晋级。Pipeline 没有被 Agent 取代，而成为容纳概率决策的确定性控制结构。

这段修订保留了用户原说明的核心洞察，同时消除了“所有入口同构、所有 Agent 同 Runtime、所有 Worker 自动有 Knowledge Graph、Scoped Token 已普遍闭合、FME 也输出 PR/Remediation、Pipeline 已有统一预算器”六类过度外推。

## 九、Presentation-ready 判断

**结论：`presentation_ready: true`，适合画成一张“Agent 被 Pipeline 与证据层包围”的公司级架构图。**

图前必须锁定以下事实标签：

1. 标题使用“分析视图”或“产品组合与控制架构”，不要写“官方六层架构”；
2. CLI 标 `Public Beta`，Worker/DevOps/MCP 等可标 `GA`，但另加“Account/Flag dependent”；
3. DevOps Agent、Worker、专项 Agent 使用不同图形或颜色，并在专项 Agent 下写“Runtime varies”；
4. Knowledge Graph 到 Custom Worker 的连接用虚线，标“按产品/显式连接可用”；
5. Pipeline 与 Runtime 拆开：Pipeline 是 Orchestration/Control，Cloud/Kubernetes/Delegate 是 Execution Plane；
6. Governance 与 Evidence 可在视觉上合并为底座，但语义分两行：上行是“谁能做/何时阻断”，下行是“结果如何被证明”；
7. Scoped Token 旁标 `Feature flag + Trigger gap + docs conflict`；Audit 旁标 `Execution events opt-in`；
8. 最终主张保持为：**Agent 负责产生候选判断与动作，Pipeline 和外部 Oracle 决定它能否晋级。**

如果单页需要比原始六层更清楚的控制关系，推荐把内容投影成六条横带，并将治理改画为贯穿全图的两侧 Trust Rails：

1. **Interaction Surfaces：** UI、IDE、MCP Client、Skills、CLI 3.0 Beta；
2. **Agent Portfolio：** DevOps Agent、Worker Agents、Code/Test/Security/FME/AI SRE；
3. **Context & Tool Plane：** 左侧 Knowledge Graph + HQL，右侧 Harness/Third-party MCP，中间不画强制先后顺序；
4. **Deterministic Control Plane：** Pipeline DAG、Sequential/Parallel、Approval、Failure Strategy、Rollback、Timeout/Retry/Max Turns；
5. **Execution Plane：** Harness Cloud Runtime 与 Customer Kubernetes Runtime，Delegate 位于 SaaS 与客户网络之间；
6. **Outcome & Evidence：** PR、Test Result、Remediation、Experiment Summary、RCA Theory → Test、Scanner、OPA、Signature/Attestation、SLO/Health。

左右 Trust Rails 分别承载 `Identity & Policy`（RBAC、Secrets、Scoped Token、OPA、Approval）与 `Observability & Accountability`（Audit、Execution Evidence、Cost/Trace）。这种画法仍保留用户的六层洞察，但不会把治理误画成只在流程末端发生的一次性步骤。

## 十、一手来源清单

### 当前官方产品文档

- [Overview of Harness AI](https://developer.harness.io/docs/platform/harness-ai/overview/) — 能力状态表、模型、GA/Public Beta。
- [Core capabilities of Harness AI](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/) — UI、IDE、Pipeline 三个工作面，DevOps Agent BYOM 限制。
- [Harness AI DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/) — UI-only、资源/OPA/GitOps、模型与用户 Accept。
- [Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) — Definition、Stage、Model Connector、MCP、Runtime、Permission、Trigger 与限制。
- [Agent permissions](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/) — Scoped Token、Stage/Step Group 传播与冲突口径。
- [Harness MCP Server](https://developer.harness.io/docs/platform/harness-ai/harness-mcp-server/) — Hosted/Open-source MCP、11 Verb、Resource Registry、Elicitation。
- [Harness Skills](https://developer.harness.io/docs/platform/harness-ai/harness-skills/) — Skills 与 MCP 的关系。
- [Code Quality Agents](https://developer.harness.io/3k-docs/ai/code-agent/) — Code Review/Coverage/Autofix 的输出与 Run Step 实现。
- [Security Agents](https://developer.harness.io/3k-docs/ai/security-agent/) — Zero Day Remediation 与 Manifest Remediator。
- [AI-based Remediations](https://developer.harness.io/docs/security-testing-orchestration/remediations/ai-based-remediations/) — STO Remediation、Code Suggestion 与 PR。
- [AI Test Automation Overview](https://developer.harness.io/docs/ai-test-automation/get-started/overview/) — AI Test、Self-healing、Kubernetes Pod。
- [AI Test Pipeline Integration](https://developer.harness.io/3k-docs/ai-test-automation/integrations/harness-cd/) — CI/CD/Custom Stage 中的测试 Step 与 Result。
- [FME Release Agent](https://developer.harness.io/docs/feature-management-experimentation/release-agent/) — FME Chat、Metric/Experiment Summary。
- [AI Scribe Agent](https://developer.harness.io/docs/ai-sre/ai-agent/) — Timeline、Key Event、Action Item、RCA 输入。
- [RCA Change Agent](https://developer.harness.io/3k-docs/ai-sre/ai-agent/rca-change-agent/) — Theory 与 Confidence Score。
- [AI SRE Runbooks](https://developer.harness.io/docs/ai-sre/runbooks/) — Triggered deterministic response automation。
- [AI SRE Support Matrix](https://developer.harness.io/docs/ai-sre/resources/whats-supported/) — Feature Flag 与 EA 条件。
- [RBAC](https://developer.harness.io/docs/platform/role-based-access-control/rbac-in-harness/) — Principal、Role、Resource Group。
- [Policy As Code](https://developer.harness.io/docs/platform/governance/policy-as-code/harness-governance-overview/) — OPA Policy 与 Enforcement。
- [Manual Approval](https://developer.harness.io/docs/platform/approvals/adding-harness-approval-stages/) — Pipeline 人工 Gate。
- [Audit Trail](https://developer.harness.io/docs/platform/governance/audit-trail/) — Audit Field 与 Execution Event Opt-in。
- [Failure Strategies](https://developer.harness.io/docs/platform/pipelines/failure-handling/define-a-failure-strategy-on-stages-and-steps/) — Retry、Manual Intervention、Abort、Rollback。
- [STO Failure Criteria](https://developer.harness.io/docs/security-testing-orchestration/key-concepts/fail-pipelines-by-severity/) — Scanner/OPA 阻断 Pipeline。
- [Continuous Verification](https://developer.harness.io/3k-docs/continuous-delivery/verify/verify-deployments-with-the-verify-step/) — Health Source、Anomaly 与 Rollback。
- [SLO-driven Rollback](https://developer.harness.io/docs/service-reliability-management/manage-slo/automated-deployment-rollback/) — Error Budget/SLO 与回滚。

### 官方工程、发布和源码

- [Deterministic by Design: Knowledge Graph + HQL](https://www.harness.io/blog/why-harness-ai-uses-knowledge-graph) — Read/Query/Analyze 与 MCP 的四级分工。
- [Introducing Autonomous Worker Agents](https://www.harness.io/blog/introducing-autonomous-worker-agents) — Pipeline-native Agent、Knowledge Graph/MCP、Cost、Chain、Governance 的厂商架构说明。
- [Worker Agents GA Press Release](https://www.harness.io/press-and-news/harness-launches-autonomous-worker-agents-for-software-delivery) — GA 发布与厂商定位。
- [harness/mcp-server](https://github.com/harness/mcp-server) — Harness 官方 MCP Server 源码。
- [harness/harness-skills](https://github.com/harness/harness-skills) — Harness 官方 Skills 源码。
