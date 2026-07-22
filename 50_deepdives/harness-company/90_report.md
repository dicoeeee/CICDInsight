---
title: Harness 公司 Agentic Software Delivery 深度洞察报告
aliases:
  - Harness Company Deep Dive
  - Harness AI Software Delivery Report
tags:
  - research/agentic-cicd
  - research/deep-dive
  - company/harness
status: complete
as_of: 2026-07-22
confidence: medium-high
---

# Harness 公司 Agentic Software Delivery 深度洞察报告

> [!abstract] 核心结论
> Harness 在 2026 年已经不只是“带 AI 的 CI/CD 厂商”，而是在把既有 Pipeline 和软件交付平台改造成 Agent 的生产控制面。它用 DevOps Agent 处理平台设计与交互，用 Worker Agents 承载 Pipeline 内多轮行动，用 Knowledge Graph/HQL 提供结构化交付上下文，用 MCP/CLI/Skills 向外部 Agent 开放能力，再由 RBAC、OPA、Approval、Scoped Token、隔离 Runtime 和 Audit 收窄风险。其产品形态和安全架构值得大型企业重点跟踪；但通用 Worker Agent 刚于 2026-06 GA，量化效果、独立红队、Marketplace 供应链和全生命周期生产自治仍缺证据；当前两份权限文档在 Principal 绑定和资源覆盖上也存在冲突。最合理的近期定位是 **L2 可审查变更 + 非生产/批准后 L3 执行**，不是关键发布的通用 L4。

## 一、研究对象与观察窗口

本报告中的 Harness 专指 [Harness Inc.](https://www.harness.io/) 及其 Software Delivery Platform，不是通用 Agent Harness 或 “Harness Engineering” 方法论。

重点观察 2025H2—2026 的发布：[2025-08 Harness AI 宣布 GA](https://www.harness.io/blog/announcing-harness-ai)；[2025-09 收购 Qwiet AI](https://www.harness.io/press-and-news/harness-strengthens-its-application-security-business-with-acquisition-of-qwiet-ai)加强 SAST/SCA/Reachability；[2025-12 与 AWS/Kiro 加深集成](https://www.harness.io/press-and-news/harness-announces-new-integrations-with-aws-to-connect-ai-software-development-with-intelligent-delivery)；2026 年先后升级 DevOps Agent、开放 MCP/Skills/IDE 能力、扩展 AI SRE/Test/Artifact/AI Cost 与 Engineering Insights；[2026-06 收购 Codecov](https://www.harness.io/press-and-news/harness-acquires-codecov)；[2026-06-30 发布 Autonomous Worker Agents 和 Agent Marketplace](https://www.harness.io/press-and-news/harness-launches-autonomous-worker-agents-for-software-delivery)。公司的扩张逻辑是把代码完成后的测试、安全、构建、制品、部署、发布、事故和成本控制点纳入同一交付语义层。

截至观察日，[Harness 公司页面](https://www.harness.io/company/about-us)自报成立于 2017 年、1,200+ 员工、1,000+ 客户。规模数据只能用于理解厂商体量，不能证明某一 Agent 能力成熟。

## 二、战略判断：不是一个 Agent，而是一套“After Code”控制面

Harness 的平台战略可以拆成六层：

~~~mermaid
flowchart TB
  E["入口层<br/>Harness UI · IDE · MCP · CLI · Skills · Event"]
  A["Agent 层<br/>DevOps Agent · Worker Agents · 专项 Agents"]
  C["上下文层<br/>Software Delivery Knowledge Graph · HQL · RAG · Memories"]
  T["工具层<br/>Harness MCP · Module APIs · Third-party MCP · Connectors"]
  X["执行层<br/>Pipeline · Agent Step · Delegate · Cloud/K8s Runtime"]
  G["治理与证明层<br/>RBAC · OPA · Approval · Audit · Test/Scan/Policy/SLO"]
  E --> A
  C --> A
  A --> T
  A --> X
  T --> X
  X --> G
  G --> A
~~~

这套结构的战略意义有三点：

1. **Pipeline 从脚本编排器变成 Agent Runtime。** Agent 是 Step，仍受原先触发、条件、Approval、Failure Strategy 和 Rollback 控制。
2. **上下文比模型更重要。** 模型可以升级，真正的平台壁垒是服务、Pipeline、部署、制品、安全、Incident、Owner 和 Policy 的关系图。
3. **治理从“写完脚本后审查”移动到 Runtime。** Agent 在运行时选择 Tool，因此身份、Scope、Tool Allowlist、调用记录和外部 Oracle 必须逐次生效。

Harness 的营销语“AI writes the code; Harness ships it”非常清晰，但它也暴露边界：Harness 的主要优势在代码完成后的交付，并不意图取代 Coding Agent、SCM 或所有专业工具。

## 三、产品组合：三类 Agent + 三类接口

### 3.1 DevOps Agent：设计和操作 Harness 平台

[DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/) 仅在 Harness UI 中使用，主要能力包括：

- 创建和修改 CI、CD、IaCM、IDP、SCS、STO、DB DevOps、Chaos 等多模块 Pipeline、Stage 和 Step；
- 创建/更新 Service、Environment、Connector 和 Secret 等资源；
- 生成并集成 OPA Rego Policy；
- 分析 CI/CD 故障、给出 Root Cause、修复建议和部分 YAML Auto-repair；
- 生成 Pipeline/Execution/依赖资源摘要；
- 查询、创建、更新和 Sync GitOps Application、ApplicationSet、Cluster、Repository 等资源。

2026 年官方将原来的 5 个 Subagent 合并为一个统一 DevOps Agent，理由是减少路由开销、改善上下文保持、模板解析和复杂 Pipeline 修改。官方称用 50 Stage Pipeline 做过验证，但没有公开任务集、一次成功率和人工修订量，因此只能视为容量信号。

DevOps Agent 使用 Harness 管理的模型，不支持 BYOM；作用域受已购买模块限制。它适合把平台工程师从“点击 UI/手写 YAML”转向“给出意图、模板、约束和验收”，不适合绕过 Save Review、OPA 和执行审批。

### 3.2 Worker Agents：Pipeline 内可复用多轮执行单元

[Worker Agents](https://developer.harness.io/docs/platform/harness-ai/harness-agents/) 是 2026 年最重要的新能力。一个 Agent 定义由以下元素组成：

- Instructions；
- Model Connector；
- 可选 MCP Server Connectors；
- Inputs/Outputs、最大轮次及运行配置；
- 独立版本、RBAC 和 Marketplace 元数据。

它作为 `Agent` Step 加入 CI、CD、IaCM、STO、SCS 或 Custom Stage。CI/STO/SCS/IaCM 可直接使用 Harness Cloud Runtime；CD/Custom 需要 Containerized Step Group；也可通过 Delegate 在客户 Kubernetes 中运行，以控制网络、数据驻留和计算资源。

Worker Agent 支持 Harness Managed 的 Anthropic/OpenAI Connector，也支持客户自带 Anthropic（直接或 AWS Bedrock）和 OpenAI 连接器。Managed LLM 使用在 2026-08 之前包含于订阅，之后将额外计费；公开文档尚未给出完整费率，试点不能按当前免费窗口外推成本。

复杂编排仍由 Pipeline 负责：

- Sequential：后一个 Agent 消费前一个输出；
- Parallel：多个 Agent 并行调查或修复；
- Conditional：根据前一步结果触发；
- Matrix：对多个 Repo、Service 或 Environment 运行同一 Agent；
- Loop/Retry：在最大轮次和 Pipeline 失败策略内迭代；
- Mixed：Agent Step 与 Test、Scan、Approval、Deploy 等确定性 Step 组合。

### 3.3 专项 Agent：围绕具体结果负责

| 领域 | 能力 | 输出/Oracle | 当前判断 |
|---|---|---|---|
| Code Repository / Code Quality PR Agents | PR Summary、Review、Coverage、AutoFix | Comment、Coverage、Branch/PR、Build | L1—L3；Run Step/Execute API/PAT 路径，需独立核验 Runtime |
| Managed Worker CI Agents | CI Autofix、Coverage 等 Marketplace Agent | PR Branch、Build 重触发、max-turns | 受限 L3；Worker Runtime，效果数据不足 |
| AI Test Automation | Intent Test、AI Assertion、Smart Selector、自愈 | 真实浏览器/API/视觉结果 | 产品成熟度较高，但需开通 |
| Security | STO/SCS Remediation、Manifest/Zero-day/Library Agent | Scanner、SBOM、Policy、PR | 适合 Analyzer + Agent |
| IaCM/CD/GitOps | Pipeline 创建、Drift/Manifest 修复、Sync | Plan、OPA、Approval、Deploy | 批准后 L3 |
| FME | Release Agent、Flag/Experiment Summary、Flag Cleanup | FME 状态、PR、实验结果 | 咨询/清理为主 |
| AI SRE | Scribe、RCA Change Agent、Investigator、Runbook | Incident Timeline、人类确认、Pipeline/Runbook | 分析 L1—L2，受控动作 L3 |
| AIDI/CCM | AI 采用、工程指标、AI/Cloud Cost、Dashboard | DORA/SPACE、成本数据 | 管理和优化入口 |

专项 Agent 不一定都基于同一 Worker Runtime。当前 Code Repository/Code Quality 文档仍展示普通 Run Step、专用 Execute API、Harness PAT/模型密钥和多组容器镜像；Marketplace Managed Worker 才按通用 Agent Definition/Runtime 运行。企业必须分别询问数据、模型、凭据、镜像、权限、版本和 SLA，不能从 Worker Agent 的隔离架构推断全部专项模块具有相同实现。

### 3.4 MCP、CLI 和 Skills：向 Harness 外部开放

[Harness MCP Server](https://developer.harness.io/docs/platform/harness-aida/harness-mcp-server/) 是官方开源接口，2026-07 文档列出 11 个通用 Tool、139 个 Resource Type 和 30 个 Toolset；[GitHub 仓库](https://github.com/harness/mcp-server)随后已显示更多 Resource Type/Toolset。数量持续漂移，企业应固定 Release，而不是依赖 `latest`。

核心 Tool 不是每个 API 一个，而是 `harness_describe/schema/list/get/create/update/delete/execute/search/diagnose` 等通用 Verb，通过 `(resource_type, operation)` 分发到 Registry。`describe` 和 `schema` 只在需要时加载字段与创建 Schema，因此 Tool 数量保持稳定，领域知识渐进进入上下文。

MCP 支持本地 `stdio`、HTTP 和 Hosted 模式；Hosted MCP 可能需要 Harness Support 按账户配置。Create/Update/Delete/Execute 使用 Elicitation 请求确认，但真正安全边界仍是 Harness RBAC、Scope、Gateway 和底层资源权限，不能把客户端确认按钮当成授权系统。

[Harness Unified CLI 3.0](https://developer.harness.io/docs/platform/harness-ai/overview/) 仍为 Public Beta；[Harness Skills](https://github.com/harness/harness-skills) 把 Pipeline 创建、操作、排障和治理方法封装给 Claude Code、Cursor、Copilot 等 Coding Agent。三者的角色是：CLI 提供可脚本化确定性接口，MCP 提供跨 Agent 的结构化发现与调用，Skill 提供工作方法；都不替代 Pipeline 控制面。

## 四、如何使用：三条典型路径

### 4.1 用 DevOps Agent 创建受治理 Pipeline

1. 账户管理员启用 Harness AI，并决定 Org/Project 是否可覆盖；
2. 用户在目标 Project 打开 Harness AI；
3. Prompt 明确模块、Connector、模板、环境、部署策略、扫描、Approval 和 Rollback；
4. DevOps Agent 读取当前页面、资源和已授权上下文，生成/修改 YAML；
5. Harness AI Rules 可提前引导输出，例如要求安全扫描和生产审批；
6. 用户检查 Diff/YAML 和引用资源；
7. OPA Policy 在 Save/Run 阶段做硬校验；
8. 保存后按原 Pipeline 权限执行，而不是由聊天结果直接获得生产权。

建议 Prompt 不只写“创建 Java Pipeline”，而要写清 Connector、Template、Stage、制品、环境、验证、失败策略和批准者。Rules 适合统一常规要求，OPA 适合不可违反的控制。

### 4.2 在 Pipeline 中运行 Worker Agent

> [!tip] 配置速查
> Worker Agent Definition 的最小 YAML、Connector 输入、运行参数和字段风险见 [[60_tutorials/harness-worker-agent-config|Harness Worker Agent YAML 配置速查]]。

1. 确认账户显示 AI → Worker Agents；若没有，联系管理员/Support；
2. 选择 Marketplace Agent 或创建 Custom Agent；
3. 配置 Instructions、Model Connector 和必要 MCP Connector；
4. 只暴露任务需要的 MCP Toolset/Tools；
5. 将 Agent 添加到目标 Stage；CD/Custom 使用 Containerized Step Group；
6. 显式配置 Resource/Verb Permission；7 月 20 日 Worker 页面称 Manual/API Run 按触发 Principal RBAC 与 Grant 求交集，但 7 月 15 日权限页给出不同身份语义，必须按目标账户实测；Webhook/Schedule/Artifact/Manifest Trigger 另行设计身份与审批；
7. 设置最大轮次、超时、成本、网络和输出；
8. 前置确定性证据收集，后置 Test/Scan/Policy/Approval；
9. 先在非生产运行，审查 Tool Call、Patch、成本和 Audit；
10. 达到评测门槛后才开放 Draft PR 或批准后动作。

### 4.3 从外部 Coding Agent 调用 Harness

1. 固定 MCP Server 版本或申请 Hosted MCP；
2. 使用 OAuth/短期凭据或最小权限 PAT/SAT，禁止共享管理员 Token；
3. 用 `HARNESS_TOOLSETS` 等方式只开放必要域；
4. Agent 先 `describe`/`schema`，再 `list/get/diagnose`；
5. Create/Execute 类动作触发确认并经 RBAC/Policy；
6. 返回 Harness Execution Link，由原平台继续记录、审批和观察；
7. 对 Skills/CLI/MCP Release 做版本、签名和回归管理。

这条路径让开发者在 Cursor、Claude Code、Kiro、VS Code 等环境中查看 Pipeline、诊断失败或触发批准的执行，但 Harness 仍是动作和审计的 System of Record。

## 五、技术原理

### 5.1 Knowledge Graph + HQL：先建模，再让模型查询

Harness 认为原始 API/MCP 不适合作为复杂分析默认路径。跨 Pipeline、Service、Deployment 和 Security 的问题若直接调用多个 API，会带来 Tool 选择、分页、Join、字段解释和大 JSON 上下文成本。

其替代方案是：

1. Type Selector 从 Schema Catalog 选择相关实体；
2. Query Builder 生成少量 HQL；
3. Knowledge Graph 使用显式字段、单位、可聚合性、Join 和 Cardinality 执行；
4. 返回结构化聚合结果；
5. 模型只做解释和摘要。

字段元数据限制可排序、可搜索、可聚合和单位；关系声明避免模型猜 `pipeline_id` 是否应与另一个 API 的 `execution_id` 关联；低温度和严格 JSON 又收窄 Type Routing。Harness 自报复杂问题可减少 15—25 倍 Token，但这是架构估算，仍需用企业真实任务复验。

Harness 给出的数据访问优先级很有参考价值：

1. 已建模 Read/Query/Analyze → Knowledge Graph；
2. 大日志/Trace → 先用 Graph 定位，再取限定内容；
3. 关键外部系统 → Managed Integration/结构化适配；
4. 长尾外部系统 → MCP；
5. Create/Update/Execute → 受治理 Tool。

### 5.2 MCP Registry：能力增长，Tool 数量不线性增长

MCP Server 分三层：

- Tool Surface：少量稳定 Verb；
- Registry/Dispatcher：路径、Scope、Auth、Pagination、Body、Response、Read-only 和 Feature Gate；
- Harness Client：HTTP、Retry、Rate Limit、Auth Header 和 Timeout。

这相当于让 Agent 调用稳定“系统调用”，把 API 细节留在适配层。`describe/schema` 是按需分页领域知识；Toolset Filtering 是更粗的暴露面控制；Knowledge Graph Views 负责跨模块分析；MCP Host 仍负责 Agent Loop 和任务状态。MCP Server 自己不是 Agent Runtime。

### 5.3 Pipeline：概率决策与确定性执行的混合图

Agent Loop 通常是 Observe → Hypothesize/Plan → Select Tool → Execute → Observe → Verify。Harness 不让这个循环替代 DAG，而是把它放进 Pipeline Node：

~~~mermaid
flowchart LR
  I["Log/Diff/Policy/Context"] --> A["Agent Step"]
  A --> P["Patch/Diagnosis/Plan"]
  P --> T["Test/Build/Scan"]
  T --> O{"Oracle Passed?"}
  O -- No, within budget --> A
  O -- No, budget exhausted --> H["Human Escalation"]
  O -- Yes --> G["OPA/Approval"]
  G --> X["Deploy/PR/Runbook"]
  X --> V["SLO/Verification/Rollback"]
~~~

确定性 Pipeline 提供边界和证明；Agent 只负责开放式理解、候选行动和有限重试。这也是 AutoFix 可到 SH3、但不应自动获得 Merge/Release 权的原因。

### 5.4 Worker Runtime：假设 Agent 已被攻陷

[Harness 2026-07-13 技术文](https://www.harness.io/blog/how-we-secured-ai-worker-agents-in-harness)披露四层隔离：

1. **Image Hardening：** 移除 Compiler、Package Manager、Setuid/Setgid 和多余网络工具；推荐只读 Root FS、独立 Scratch、Drop Capability、禁用 Privilege Escalation。
2. **Process Isolation：** Agent、Credential Broker、Egress Proxy 在同一 Container 中由三个不同非特权用户运行，Kernel/File Permission 隔离 Broker 文件和源码。
3. **Secret Isolation：** 启动前将疑似 Secret 替换为单次 Placeholder；真实值只在 Broker 中；请求发往绑定 Host 时才注入；Response 也做 Secret Scrub。
4. **Network Isolation：** 所有出口强制经过 Allowlist Proxy，默认拒绝；Cloud Metadata 和非目标 Host 应被阻断。

Harness 称回放某 CVSS-9.0 攻击后，环境变量输出从 709 个含真实 Secret 降至 33 个无可用凭据，并把隔离测试作为 Image Release Gate。它说明设计有工程化测试，但仍是第一方声明；客户应验证自己使用的 Image、CPU 架构、Delegate/K8s 配置、DNS/IPv6/Sidecar 和 MCP 路径。

### 5.5 身份与授权：目标是委托身份，但当前文档仍冲突

[2026-07-16 身份文章](https://www.harness.io/blog/identity-and-permissions-for-ai-worker-agents-in-harness)与 7 月 20 日 Worker 页面给出的目标模型是：

> Agent 没有常驻特权；有效访问是触发 Principal 权限的有界子集，只在一次 Run 内存在。

控制分为两阶段：

| 阶段 | 控制 | 作用 |
|---|---|---|
| Save/Trigger 前 | Agent Resource RBAC | 谁能 Author、Publish、Execute、Attach Connector |
| Save/Trigger 前 | OPA | 哪些 Model、Connector、Turn、Variable 和 Permission 允许 |
| Runtime | Scoped Ephemeral Token | 7/20 Worker 页面称 `parent RBAC ∩ declared grant`；7/15 权限页对身份绑定有不同描述 |
| Runtime | MCP Gateway Tool Scope | `connector.allowedTools ∩ agent.allowedTools` |
| Runtime/After | Per-call Attribution | Agent、Run、Principal、Tool、参数、结果 |

Agent 具有独立的 Agent/Run 身份用于审计。按 7 月 20 日 Worker 页面，存在 invoking principal 时动作应在其 RBAC 与声明 Grant 的交集内；但 7 月 15 日 [Agent permissions](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/) 页面又称 Runtime Token 独立于 Pipeline Author，并只列出更窄的资源支持。两份当前文档不能同时作为同一版本的精确事实，采购与授权必须以目标 Cluster 中的 Token Subject、Scope、Expiry 和 Audit Principal 实测为准。

此外，不声明 `permissions` block 并不等于零权限：7 月 20 日 Worker 文档列出多模块默认只读 Permission；一旦声明 block，默认项不再合并，Managed LLM Connector 还需要显式 `ai_llm_gateway: access`。Permission 位于 CI/STO/SCS/IaCM Stage 或 CD/Custom Containerized Step Group，Scoped Token 会注入组内每个 Step。Agent 应使用最小独立 Stage/Step Group，避免旁路脚本或插件共享不必要权限。

> [!danger] 事件 Trigger 不是同一个授权模型
> 当前 Worker 文档注明，Pipeline Trigger 发起的 Agent Run 不会注入 scoped token，Webhook、Schedule、Artifact、Manifest 等触发方式因此不能把声明 Grant 解析为某个触发 Principal 的权限子集；该模型目前主要适用于存在 Principal 的手动/API 触发。事件驱动生产 Agent 必须单独验证运行身份、Connector Credential、审批和归因，不能把“始终继承触发人”当作现状。

另一个限制是细粒度 Permission/Token Injection 在文档中仍受 `HARNESS_TOKEN_INJECT` Feature Flag 控制。采购时必须同时在目标 Cluster 和不同 Trigger 类型实测，不能只引用架构博客。

### 5.6 Rules、Policy 和 Oracle：三层约束

- **Harness AI Rules：** Markdown 自然语言规则，影响生成结果；是软约束；
- **OPA Policy：** Save/Run 时可 Warn/Block；是平台硬门禁；
- **External Oracle：** Test、Coverage、Scanner、SBOM、Signature、Plan、SLO 和业务断言；定义结果是否正确。

三者不可互换。Rules 能让生成 YAML 更接近标准，但不能证明它合规；OPA 能证明规则表达的结构满足要求，但不能证明业务行为正确；外部 Oracle 验证运行结果，却也必须防止被 Agent 修改。

2026-07-21 发布的 AgentTrace 与开源 [`harness-evals`](https://github.com/harness/harness-evals)把 Model/Tool/Retrieval Trace、生产 Failure Dataset、Threshold 和 Exit Code 接入 CI，可作为 Agent/Prompt/Model 的回归 Oracle；Harness AI Evals SaaS 同期仍标为 Beta。概率 Eval 或 LLM Judge 只能补充传统 Oracle，不能替代 Build、Test、Scanner、OPA、Signature、SLO 和人工 Review。

[Zero Trust Service 工程文](https://www.harness.io/blog/building-a-zero-trust-service-for-ci-cd-how-we-intercept-every-task-before-it-executes)还披露了客户控制的 Task 前置 Validator Chain 与 fail-closed 参考架构。它强化了“外部控制决定 Agent 能走多远”的方向，但当前属于工程披露/联系 Account Team 的可选控制，不能写成所有 Worker Run 的默认 GA Gate。

### 5.7 数据与模型边界

[Core Capabilities](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/)称 DevOps Agent/Chat 通过 Harness Managed Provider 调用模型，不支持 BYOM；Worker Agents 则可使用客户 Model Connector。官方 AI 总览在观察日列出 Claude Opus 4.6，但同页 FAQ 和较旧 Support 文档仍保留其他模型版本，说明自动升级与文档同步是运营风险。

[2026 Subscription Terms](https://www.harness.io/legal/subscription-terms)规定 Harness 不得用 Customer Data 训练/改进第三方模型或 Harness AI，且 Output 需要 User Review；[AI Data Privacy 页面](https://www.harness.io/legal/harness-ai-data-privacy)又区分即时丢弃的 Submission Data 和可用于产品分析的 Engagement/Usage Data。企业应依据最新 Order Form、DPA、Subprocessor、区域和具体模块确认，而不是只引用“零保留”营销短句。

## 六、沿 CI/CD 阶段的覆盖

| 阶段 | Harness 已发布能力 | 近期自治建议 | 主要边界 |
|---|---|---|---|
| 1 评审/质量 | PR Summary、Semantic Search、Code Review | L1—L2 | 评论正确率、噪声、SCM 范围 |
| 2 静态/安全/合规 | OPA 生成、STO/SCS 修复、Zero-day/Manifest Agent | L2，沙箱 L3 | 必须由 Scanner/Policy 复验 |
| 3 测试/门禁 | Coverage Agent、AI Test Copilot、Playwright、Smart Selector | 测试沙箱 L3 | 自愈可能错误命中或掩盖回归 |
| 4 编译/构建 | Error Analyzer、PR Agent AutoFix；Marketplace Worker 可重触发 Build | SH2—SH3 | 实现路径不同；只覆盖可修、可复现失败；不自动 Merge |
| 5 制品/供应链 | Artifact Registry、Dependency Firewall、SCS、MCP 资源 | L1—L2 | Agent 晋级/签名自治证据有限 |
| 6 IaC/部署 | IaCM Pipeline、Drift/Manifest Remediation、GitOps Sync | 批准后 L3 | Plan、具体资源、环境和回退 |
| 7 发布/变更 | FME Release Agent、Flag Cleanup、Approval、Pipeline | L2—L3 | Release Agent 偏知识/摘要；关键发布仍保守 |
| 8 发布后/SRE | Scribe、RCA Change Agent、Investigator、Runbook | 分析 L1—L2；批准动作 L3 | 多项需 Support/Flag/EA；Runbook 需预定义 |

Harness 的覆盖广度是真实优势，但最强产品证据仍在 Pipeline、测试和受控配置；制品晋级、版本决策和关键发布的通用自治证据较弱。

## 七、应用实践与效果证据

### 7.1 Worker Agent 首发案例

- **Verint：** 官方首发引语称 4 天构建 Kubernetes/Pipeline Troubleshooting Agent，计划让约 200 名运维和约 1,000 名开发者受益。它证明创建和组织推广意向，尚不证明规模化效果。
- **United Airlines：** 官方称 4 天构建 RiskSentinel 安全修复 Agent。它证明客户可利用平台治理构建专项 Agent，但未披露生产权限、独立红队和长期结果。
- **Workday：** 2026-03 宣布采用 Harness 加强交付、测试、验证和安全。这是大型企业选型信号，不是 Worker Agent 量化案例。

### 7.2 AI Test Automation 案例

- [Gameopedia](https://www.harness.io/case-studies/gameopedia)：厂商案例称维护下降 40% 以上、每人每天节省 2—3 小时、上手从约 30 天降至 2—5 天；案例发布时 CI/CD 集成仍是下一步。
- [Wasimil](https://www.harness.io/case-studies/wasimil)：厂商案例称测试失败由约 50% 降至不足 10%、维护从约 2 小时/日降到 45—60 分钟、发布从每周两次到每天。
- [Siemens Healthineers](https://www.harness.io/case-studies/siemens-healthineers)：PoC 称测试创建由多日降至不足 1 小时，调试维护由小时降至分钟；未来 KPI 仍待验证。

这些数据比通用 Agent 营销更具体，但均是供应商选择并发布的案例。企业还需测 False Pass、缺陷逃逸、业务断言、长期漂移和总体测试成本。

### 7.3 AI SRE：从理解到预定义行动

AI Scribe 监听 Slack、Zoom、Teams，生成关键事件、行动项和六段式 Postmortem；RCA Change Agent 将时间线与 Deployment、PR、ServiceNow Change 等关联，持续更新 Root Cause Theory 和 Confidence；Runbook 负责触发 Harness/Jenkins/GitHub Pipeline、Feature Flag、通知和人工步骤。

这是一种合理的快慢双环：Agent 负责整理和提出理论，Runbook 负责预定义动作，SLO/人类负责验证。需要校准状态：Scribe 详细文档仍要求 Support 开通，Postmortem 有 Feature Flag，Investigator Agent Pipelines 是 Early Access，不能把整个 AI SRE 套件当成无条件 GA。

## 八、成熟度和证据校准

### 8.1 状态必须分三栏

| 能力 | 官方 Capability 状态 | 实际获得条件 | 生产证据 |
|---|---|---|---|
| DevOps Agent | GA | Enterprise/模块范围；SaaS；Managed Model | 厂商测试 + 产品文档 |
| Worker Agents/Marketplace | GA | AI Agents 启用；部分账户/权限需 Support/Flag；事件 Trigger 身份另验 | 首发客户引语，量化数据少 |
| Harness MCP | GA | Hosted 可能需账户配置；自托管可用 | 开源实现和文档较强 |
| Unified CLI 3.0 | Public Beta | 安装和认证 | 仍需兼容性运营 |
| Code Quality PR/API Agents | 文档化可用 | Run Step、Execute API、PAT/LLM Key、SCM 与镜像配置 | 缺跨客户成功率；不能继承 Worker 安全结论 |
| Managed Worker CI Agents | Worker/Marketplace GA 口径 | AI Agents 启用、Agent Definition、Runtime 与 Permission 条件 | Build Loop 机制已披露，量化效果少 |
| AI Test Copilot | 总览 GA | 完整模块需销售/团队开通 | 三个署名案例，仍为第一方 |
| AI Scribe | 总览 GA | 详细文档要求 Support | 机制清晰，独立效果少 |
| Investigator Pipelines | EA | Harness Representative 启用 | 不应承诺生产 SLA |
| Scoped Agent Permissions | 设计已公开但文档冲突 | `HARNESS_TOKEN_INJECT` Flag；默认只读 Permission；Stage/Group Token 传播；Trigger Run 无触发人 Token | 必须按账户、资源与 Trigger 类型实测 |
| AgentTrace / `harness-evals` | 开源/工程能力可用 | Trace、Dataset、Threshold、Exit Code | 适合 Agent 回归，不替代业务 Oracle |
| Harness AI Evals | Beta | 申请加入 Beta | Native Pipeline Gate 尚不能写成 GA 基线 |
| Zero Trust Service | 工程披露/受控接洽 | 客户 Validator、Task 前置、fail closed | 非 Worker 默认组成，需 Account Team 确认 |

### 8.2 目前可以确认什么

- Harness 已形成跨 UI、IDE 和 Pipeline 的完整产品组合；
- Worker Agent 是 Pipeline 原生 Step，可复用模型和 MCP；
- Knowledge Graph/HQL、MCP Registry 和 Pipeline 的职责划分清晰；
- Worker Runtime 和委托权限架构已披露较具体技术细节；
- 测试产品已有署名客户和可量化方向性结果；
- 公司已获得 Workday、United、Verint 等大型企业采用信号。

### 8.3 目前不能确认什么

- Worker Agent 的行业平均“自愈率”或跨企业成功率；
- Marketplace Agent 均达到生产安全和质量门槛；
- 关键生产发布普遍实现无人值守 L4；
- Harness 自报 Token 节省、隔离红队和测试 Benchmark 可独立复现；
- 所有 GA 功能在任一 Harness Account/Region/SKU 中立即可用；
- Webhook/Schedule/Artifact/Manifest Trigger 已能安全继承某个用户的 scoped permission；
- Self-hosted Worker Runtime 等于整套 Harness AI 可私有化部署。

## 九、流程与人员变化

### 平台工作流

| 过去 | Harness Agentic 路径 |
|---|---|
| 人手写 Pipeline/YAML | 人声明意图，DevOps Agent 生成，Rules/OPA/Review 定型 |
| Script 预先确定动作 | Worker Agent 运行时选 Tool，Pipeline/Scope 限定可能动作 |
| 日志靠人串联 | Knowledge Graph/HQL/MCP 聚合结构化上下文 |
| Bot 使用共享 Token | 每 Run 委托身份 + 权限子集 + Per-call Attribution |
| 事故靠人记时间线 | Scribe/RCA 整理证据，Runbook 执行预定义动作 |
| 测试依赖静态 Locator | Intent + DOM/Vision + Smart Selector + Browser Oracle |

### 人员能力

- **平台工程师：** 从维护每条 YAML 转向 Template、Graph、Agent Catalog、OPA、评测和预算运营；
- **开发者：** 从查日志和手工修 CI 转向描述验收条件、审查 Patch/证据和处理 Unknown；
- **QA：** 从 Locator 维护转向业务断言、False Pass、长期漂移和测试风险模型；
- **安全/IAM：** 管理 Agent Resource、委托身份、MCP Tool Scope、Marketplace 供应链和红队；
- **SRE：** 把隐性经验转成 Incident Type、Runbook、SLO、验证与回退；
- **管理者：** 从采用率转向每成功任务成本、交付稳定性、缺陷逃逸和风险收益。

## 十、竞争边界

| 平台 | 核心优势 | 相对 Harness 的强项 | 相对 Harness 的弱项 |
|---|---|---|---|
| GitHub Agentic Workflows | Repo/PR/Actions 原生，Markdown 编译和 Safe Outputs | 开源 Workflow、仓库分发、Actions 生态 | 跨测试/SRE/FinOps/Artifact 语义层较弱 |
| GitLab Duo | 一体化 DevSecOps、Self-Managed/BYOM | 代码到安全数据面统一、私有化 | Worker Runtime 隔离细节和多模块广度需逐项比较 |
| AWS DevOps Agent | Cloud Topology、Telemetry、跨账号生产上下文 | AWS 运维调查深度 | 官方本体不代操作员执行 Remediation；交付平台跨云性较弱 |
| Azure/GCP | 云与开发工具生态 | 云原生上下文、模型/IDE 分发 | 端到端交付控制面分散 |
| 专业测试/SRE/安全厂商 | 强领域 Oracle | 特定任务准确性和资产深度 | 跨阶段编排、统一身份/Policy 需外部平台 |
| 独立 Coding Agent | 跨平台、开发者体验、模型选择 | 本地代码修改灵活、部署简单 | 缺企业交付 Graph、生产 Pipeline 治理和专项 Oracle |

Harness 的差异化不是某一个 Agent 比别人“更聪明”，而是把 Agent 放入企业已经用于部署的 Pipeline，并用多模块交付数据和统一治理扩张其行动面。代价是更强平台依赖和更高采购/迁移复杂度。

## 十一、企业建议

### 值得优先试点

1. DevOps Agent 生成/修改非生产 Pipeline 和 OPA 草案；
2. 只读 MCP Pipeline Diagnosis；
3. CI AutoFix Draft PR，原 CI 全量复验；
4. AI Test Selector Drift 与业务断言组合；
5. AI SRE Scribe/RCA，Runbook 保持人工触发或批准；
6. Manifest/IaC Remediation 的 Dry-run/Plan。

### 暂不建议

- 给通用 Worker Agent 共享管理员 Token；
- 让 Community Agent 直接进入生产；
- 用 AI Rules 代替 OPA/Approval；
- Agent 同时修改 Test/Gate 并用其证明成功；
- 依赖 `latest` MCP/Agent Template/模型而不回归；
- 将 Smart Selector 自愈作为关键交易唯一 Oracle；
- 自动 Merge、Artifact Promote、Production Deploy/Destroy 或开放式 Incident Remediation。

### 采购门槛

- 在目标 Cluster 验证 Worker 权限 Feature Flag、Hosted MCP、AIT/Scribe 可用性；
- 分别验证 Manual、API、Webhook、Schedule、Artifact、Manifest Trigger 的 Principal、Token、Connector、Audit 和失败清理；
- 固定并导出 Agent、MCP、Model、Connector 和 Tool Call 版本；
- 用内部任务集验证 Knowledge Graph 数据新鲜度与 HQL Join；
- 复现 Secret/Network/Token 红队；
- 按 Failure Class 统计 Verified Fix，不接受单一“成功率”；
- 确认 2026-08 后 Managed LLM 费率和总成本；
- 要求 Marketplace Agent 的签名、SBOM、评测、变更和回滚证据。

完整实施步骤见 [[50_deepdives/harness-company/60_playbook|企业 Playbook]]，实验计划见 [[50_deepdives/harness-company/40_labs/README|Labs]]。

## 十二、未来 12—18 个月判断

1. **Worker Agent 会成为 Harness 新的横向平台原语。** Code Quality、Security、IaC、Feature Flag 等现有 Agent 将逐步以 Marketplace/Worker 形式统一，但短期仍会保留多套实现。
2. **Knowledge Graph 与 MCP 会分工深化。** Graph 处理授权后的结构化理解，MCP 处理动作和长尾扩展，CLI/Skills 提供开发者本地入口。
3. **治理重点从 Pipeline Author 转向 Runtime Caller。** Scoped Delegation、Tool Intersection、Call Attribution、Agent Supply Chain 和模型版本将成为企业采购核心。
4. **测试和 CI 修复先规模化。** 因为能在沙箱中由浏览器/CI 复验；制品晋级和生产发布仍会保守。
5. **计费会从许可证扩展到 Agent 消耗。** Managed LLM、Runner、并发、模块调用和长期 Graph/Log 数据将共同决定经济性。
6. **最大风险是产品速度快于企业运营。** 文档、模型、Schema、Agent Template 和 Feature Flag 快速变化会迫使平台团队建立版本固定和持续评测。

## 最终判断

Harness 是 2026 年 Agentic CI/CD 最值得关注的平台厂商之一，因为它已经具备多数 Coding Agent 缺少的四个条件：生产 Pipeline Runtime、跨模块交付上下文、企业权限/Policy，以及测试/安全/SRE 等外部 Oracle。其 Worker Agent 的隔离和委托权限设计也比只依赖 Prompt Approval 的方案更接近生产要求。

但“具备生产架构”与“已经证明通用生产自治”仍有距离。今天最可行的企业路径是：

> 用 Harness 让 Agent 进入受控 Pipeline，但让 PR、Test、Scan、Policy、Approval、SLO 和 Rollback 决定它能走多远。

## 主要来源

- [Harness AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/)
- [Harness AI Core Capabilities](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/)
- [Harness DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/)
- [Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)
- [Agent Permissions](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/)
- [Harness MCP Server](https://developer.harness.io/docs/platform/harness-aida/harness-mcp-server/)
- [MCP Open-source Repository](https://github.com/harness/mcp-server)
- [Knowledge Graph/HQL Architecture](https://www.harness.io/blog/why-harness-ai-uses-knowledge-graph)
- [MCP Agent Loop Architecture](https://www.harness.io/blog/agent-loop-new-os)
- [Worker Agent Isolation](https://www.harness.io/blog/how-we-secured-ai-worker-agents-in-harness)
- [Worker Agent Identity and Permissions](https://www.harness.io/blog/identity-and-permissions-for-ai-worker-agents-in-harness)
- [Code Quality Agents](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/)
- [Code Repository AI Agents](https://developer.harness.io/docs/code-repository/pull-requests/ai-agents/)
- [AgentTrace](https://www.harness.io/blog/introducing-agent-trace)、[AI Evals](https://www.harness.io/blog/introducing-ai-evals) 与 [harness-evals](https://github.com/harness/harness-evals)
- [Zero Trust Service](https://www.harness.io/blog/building-a-zero-trust-service-for-ci-cd-how-we-intercept-every-task-before-it-executes)
- [AI Test Automation](https://developer.harness.io/docs/ai-test-automation/get-started/overview/)
- [AI SRE](https://developer.harness.io/3k-docs/ai-sre/get-started/overview/)
- [Harness Subscription Terms](https://www.harness.io/legal/subscription-terms)
