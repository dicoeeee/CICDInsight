---
title: Harness CI Agent 深度洞察检验与补充
aliases:
  - Harness CI Validation 2026-07-22
tags:
  - research/agentic-cicd
  - research/deep-dive
  - research/validation
  - company/harness
  - stage/ci
status: complete
as_of: 2026-07-22
topic_id: harness-company
topic_type: company
scope: harness-ci-agents
source_policy: official-primary-only
confidence: medium-high
presentation_ready: true
---

# Harness CI Agent 深度洞察检验与补充

> [!abstract] 审计结论
> 现有 Harness 专题的核心判断经核验仍成立：Harness 的差异化不是让模型替代 CI Pipeline，而是把 Agent 放进 Pipeline，并让 Build、Test、Scan、OPA、Approval 和 PR Review 决定结果是否能够晋级。最可信自治等级仍是 **L2 可审查变更 + 受限或批准后 L3**，公开证据不支持通用生产 L4。
>
> 但 CI 部分需要三处重要收窄。第一，DevOps Agent、通用 Worker Agent、Code Quality 专项 Agent 是不同执行面，不能互相继承 Runtime、身份和权限结论。第二，2026-07-20 更新的 Worker Agents 页面仍保留 `HARNESS_TOKEN_INJECT` 和事件 Trigger 无 scoped token 的限制，同时新增默认只读权限、Managed LLM Gateway 权限和 Stage/Step Group 级权限语义；“无显式权限即无权限”已不准确。第三，当前两份官方权限文档对 Token 是否继承触发 Principal、支持资源范围及默认行为存在直接冲突，必须以目标账户、Cluster、Trigger 类型和实际审计记录验收，不能把 2026-07-16 架构博客当作普遍已落地事实。

## 一、检验范围与证据口径

本次完整复核了 [[50_deepdives/harness-company/README|专题 README]]、Charter、Question Tree、Evidence Map、Findings、Report、`research-evidence.md`，以及 `00_sources/briefs/2026-harness-*.md` 的七份 Source Brief。增量核验只使用 Harness 官方文档、官方工程博客、官方发布材料和官方 GitHub 仓库，观察截止日为 2026-07-22。

本文件区分四种证据：

| 标记 | 证据 | 可支持的结论 |
|---|---|---|
| A | 当前官方产品文档、官方仓库 | 配置、接口、限制和实现存在性 |
| B | 官方工程博客、发布稿 | 架构意图、发布时点和第一方实现说明 |
| C | Harness 托管客户案例或客户引语 | 该客户的方向性采用或结果 |
| Gap | 未公开或文档冲突 | 不能作生产承诺，需账户或实验验证 |

## 二、逐项 Claim Check

| ID | 既有 Claim | 2026-07-22 核验 | 结论 | 精确边界 / 证据 |
|---|---|---|---|---|
| CI-V01 | DevOps Agent 能设计和修改多模块 Pipeline、资源和 OPA Policy | 当前文档继续支持；还包括失败分析、YAML Auto-repair、Pipeline Summarizer 和 GitOps 操作 | **正确，但应限定为 UI 控制面 Agent** | DevOps Agent 只在 Harness UI 使用；用户需要查看 before/after YAML 并选择 `Accept` 应用修复。它不是 CI Stage 内的 Agent Step，也不直接替代 Build/Test/Scan。[DevOps Agent，2026-07-13](https://developer.harness.io/3k-docs/ai/devops-agent/)（A） |
| CI-V02 | DevOps Agent 为 Pipeline “运行时 Agent” | 它能读取执行历史、分析失败和操作 GitOps，但当前文档没有把它描述成与 Worker Agent 相同的容器化 Step Runtime | **需收窄** | 更准确表述是“设计/运维控制面 + 执行后诊断入口”；执行期多轮 Agent Step 是 Worker Agent。DevOps Agent 使用 Harness 管理的 Claude Opus 4.6；不能把 Worker Agent 的 BYOM、隔离和 scoped-token 机制套给它。[Harness AI Overview，2026-07-22](https://developer.harness.io/docs/platform/harness-ai/overview/)（A） |
| CI-V03 | Worker Agent 是 Pipeline 原生、可复用的 Agent Step | 当前文档继续明确支持 | **正确** | 一个 Worker Agent 由 Instructions、Model Connector、可选 MCP Connectors 和 Inputs 组成，可加入 CI、CD、IaCM、STO、SCS、Custom Stage。[Worker Agents，2026-07-20](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)（A） |
| CI-V04 | Worker Agent 可在所有 Pipeline Stage 直接运行 | 当前明确列举的 Stage 有限，且 Runtime 方式不同 | **需收窄** | Harness Cloud Runtime 仅明确支持 CI、STO、SCS、IaCM；CD/Custom 必须放入 Containerized Step Group；也可经 Delegate 在客户 Kubernetes 运行。不能从“跨生命周期”营销语推导任意 Stage 均同构支持。（同上，A） |
| CI-V05 | Worker Agent 自动获得 Harness 全局上下文 / Knowledge Graph | 通用 Agent Definition 的文档入口是 Instructions、表达式、Inputs、Workspace/文件、环境变量和显式 MCP Connector；没有证明任意自定义 Worker 自动装载全量 Knowledge Graph | **需收窄** | Inputs 可来自上游 Step Output、Trigger 或手工值；Harness Expressions 在执行时解析；MCP Connector 是可选且需要显式配置。发布博客称 Managed Agent 以 Knowledge Graph 为组织记忆，但这不是通用 Custom Agent 的文档化不变量。[Agent Reference，2026-07-15](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents-references/)（A）；[首发博客，2026-06-30](https://www.harness.io/blog/introducing-autonomous-worker-agents)（B） |
| CI-V06 | Worker Agent 可以调用 Harness 与第三方 MCP 工具 | 正确，但“Connector 已存在”不等于工具自动授权或自动有上下文 | **正确，需明确双边界** | Agent Definition 必须显式绑定 MCP Connector；当前 Worker 文档还注明 Connector Catalog 中的 GitHub MCP Connector 仅供 AI Chat，不能直接给 Worker Agent 使用，Worker 需 Harness Hosted MCP 或自定义 Endpoint。第三方 Tool Intersection 目前主要由 7 月 16 日工程博客披露，产品文档未给出同等粒度配置参考。[Worker Agents，2026-07-20](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)（A）；[Identity & Permissions，2026-07-16](https://www.harness.io/blog/identity-and-permissions-for-ai-worker-agents-in-harness)（B） |
| CI-V07 | Worker 有 `triggering principal RBAC ∩ declared grant` 的 scoped token | 最新 Worker 页面仍这样描述，但另一份当前官方权限页给出不同身份语义 | **机制方向成立，落地状态冲突** | 7 月 20 日 Worker 页称按 invoking principal 求交集；7 月 15 日 Agent permissions 页却称 Runtime Token 独立于 Pipeline Author，并取代通过 MCP Connector 继承 Author Credential 的默认行为。两页不能同时作为同一版本的精确事实；见“冲突与漂移”。（A + Gap） |
| CI-V08 | 未声明 `permissions` 即无 Ambient Permission | 7 月 20 日 Worker 页新增了默认只读权限表 | **已过时 / 必须改写** | 没有 `permissions` block 时，Core、Artifact Registry、Code Repository、Harness AI、CCM、FME、IaCM、IRO、STO 会注入一组默认只读 Permission；Managed LLM Gateway 权限也默认存在。声明 `permissions` 后，默认项不合并，Managed Connector 还需显式添加 `ai_llm_gateway: access`。因此应写为“无未声明的写权限，但存在文档化默认读权限”。[Worker Agents，2026-07-20](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)（A） |
| CI-V09 | 权限只约束 Agent Step | 当前权限放在 CI/STO/SCS/IaCM 的 Stage 或 CD/Custom 的 Containerized Step Group | **需补充 Blast Radius** | Scoped Token 会注入该 Stage 或 Step Group 中的**每个 Step**，不是只给 Agent 内核。混合确定性 Step 与 Agent Step 时，应拆最小 Step Group/Stage，避免把 Agent 所需写权限传给旁路脚本或插件。[Agent permissions，2026-07-15](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/)（A） |
| CI-V10 | Webhook、Schedule、Artifact、Manifest Trigger 可运行 Worker，并继承触发人权限 | 前半句正确，后半句仍不成立 | **原专题判断继续成立** | 所有标准 Trigger 可提供 `<+trigger.*>` 上下文；但 Trigger-initiated Run 当前不注入 scoped token，声明权限无法对某个 invoking principal 求交集，支持仍在 Roadmap。事件驱动写操作必须另验身份、Connector Credential、Approval 与 Attribution。[Worker Agents Limitations，2026-07-20](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)（A） |
| CI-V11 | 第三方 MCP 的有效工具集为 `connector.allowedTools ∩ agent.allowedTools`，并逐次记录 | 7 月 16 日官方工程文继续明确该架构；当前 Worker 产品页没有同等字段参考 | **正确但证据等级应保持 B** | 可作为 Harness 声明的 Server-side Gateway 设计，不应写成任意账户已验收的 A 级事实。还需验证 Connector 无 Allowlist 时的默认、Blocked Call 日志、参数/结果脱敏和第三方系统中的调用归因。[Identity & Permissions，2026-07-16](https://www.harness.io/blog/identity-and-permissions-for-ai-worker-agents-in-harness)（B） |
| CI-V12 | Worker Runtime 有容器/VM、进程、Secret Broker、Egress 四层隔离 | 最新官方资料继续支持 | **正确，仍是第一方证据** | Worker 产品页确认独立 Docker Container + isolated VM；四层细节与 CVSS-9.0 回放来自工程博客。没有公开专项第三方审计、完整 Pod Spec/SBOM 或复现实验，不提升为“已证明安全”。[Worker Agents，2026-07-20](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)（A）；[Isolation，2026-07-13](https://www.harness.io/blog/how-we-secured-ai-worker-agents-in-harness)（B） |
| CI-V13 | Code Review、Coverage、AutoFix 是一个共享 Worker Runtime 和权限模型的专业 Agent 链 | 官方文档显示至少有两套入口/实现 | **不成立，必须拆开** | Code Repository PR Agents 由普通 Run Step 调用 `/gateway/agents/api/v1/agents/.../execute`，显式传 Anthropic Key、Harness PAT 和 Git Connector；Code Quality 文档示例还使用多组容器镜像与脚本。Marketplace 的 Managed Worker Agent 则使用通用 Agent Definition/Runtime。它们职责相似，但不能共享安全结论。[PR Agents，2026-07-08](https://developer.harness.io/docs/code-repository/pull-requests/ai-agents/)（A）；[Code Quality Agents，2026-07-15](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/)（A）；[Worker 首发，2026-06-30](https://www.harness.io/blog/introducing-autonomous-worker-agents)（B） |
| CI-V14 | Code Review Agent 负责 PR 评审，Coverage 生成测试，AutoFix 修 CI 并交付 PR | 当前文档继续支持 | **正确，需按实现区分输出** | Code Review 在 PR 发布评论；Coverage 生成并验证测试后建 PR；传统 AutoFix API 读取 Execution/Repo、验证修复并建 PR；Marketplace CI Autofix 版本另称会重触发 Build 并循环至通过或 max-turns。两种 AutoFix 不能混写为一个固定流程。（同上，A/B） |
| CI-V15 | Coverage 的 90% 总覆盖、80% 单文件覆盖证明能力效果 | 当前 Code Quality 文档将其写为 Target，且明确 Go verification | **需收窄** | 这是 Agent 配置目标，不是跨语言、跨仓库实际达成率。最多证明工作流支持“生成测试—运行—Coverage 报告—PR”，不能当 Benchmark。[Code Quality Agents，2026-07-15](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/)（A） |
| CI-V16 | AutoFix = 自动合并 / 无人值守 CI 自愈 | 所有正式 CI 文档最终交付仍是 Branch/PR，开发者 Review/Merge | **不成立** | Marketplace Worker 可在 PR Branch 内重触发 Build；这只证明受限修复循环，不证明可修改 Main、跳过 Branch Protection 或自动晋级制品。[PR Agents，2026-07-08](https://developer.harness.io/docs/code-repository/pull-requests/ai-agents/)（A） |
| CI-V17 | Build、Test、Scan、OPA、Human Review 是外部 Oracle | 当前官方产品文档继续支持这种系统边界 | **正确，是最稳定结论** | STO 可运行/摄取 Scanner 并按 Policy 停止 Pipeline；OPA Policy Set 可在 Save/Run/Step Start 或 Policy Step 评价并 Error/Exit；Approval 可放在 CI Build 与 Integration Test 之间；PR Agent 输出由开发者 Review/Merge。[STO](https://developer.harness.io/docs/security-testing-orchestration/)（A）；[OPA，2026-07-20](https://developer.harness.io/docs/platform/governance/policy-as-code/harness-governance-overview/)（A）；[Manual Approval，2026-06-09](https://developer.harness.io/docs/platform/approvals/adding-harness-approval-stages/)（A） |
| CI-V18 | Harness 已有公开证据证明 Worker Agent 普遍提高 CI 成功率或 MTTR | 截至观察日仍只有首发客户引语/采用计划；没有 Verified Fix Rate、误修率或长期对照数据 | **缺证据** | United/Verint 证明四天构建和推广意向；不能证明跨客户效果。未找到公开、署名的 Worker CI Autofix 长期量化案例。[GA 发布稿，2026-06-30](https://www.harness.io/press-and-news/harness-launches-autonomous-worker-agents-for-software-delivery)（C） |

## 三、CI 设计时与运行时职责图

| 执行面 | 设计时职责 | 运行时职责 | 不应推导的能力 |
|---|---|---|---|
| DevOps Agent | 在 UI 中生成/编辑 CI Stage、Step、Pipeline、资源和 OPA 草案；配置 Failure Strategy 等 | 分析已失败 Execution、推荐修复、经用户 `Accept` 修改 Pipeline YAML；执行 GitOps 查询/Sync 等受权限动作 | 不是 CI 容器内 Worker；不自动继承 Worker BYOM、VM 隔离或 scoped-token |
| Worker Agent | 定义 Instructions、Model、Inputs、MCP、Outputs、Turn/权限；作为 Agent Step 编入 Pipeline | 在隔离 Runtime 中多轮读取文件/输入、调用工具、写 Output；Pipeline 负责条件、并行、重试、Approval、Failure Strategy | 不是 Build/Test/Scan 的替代品；Output Recommendation 不自动成为 Gate |
| Code Review 专项 | 配置 PR、Repo、模型/密钥和评论链路 | 分析 Diff，发布 Review Comment/Recommendation | 评论不等于 SCM Required Review，也不证明代码可合并 |
| Code Coverage 专项 | 配置 Repo、Branch、Coverage 目标和模型 | 生成/运行测试、生成 Coverage Report、提交 PR | 90%/80% 是目标，不是已证明结果；新增测试不能单独证明业务正确 |
| AutoFix 专项 | 在失败路径配置 API/Agent、Execution ID、Repo/Branch、最大轮次 | 诊断日志、修改 PR Branch；Worker 版本可重触发 Build 并迭代；最终创建/更新 PR | 不等于自动 Merge、允许改 Gate、自动 Promote Artifact 或生产发布 |

## 四、2026-07-16 之后的最新补充

### 4.1 Worker 权限文档新增默认权限与更大资源表

[2026-07-20 Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) 页面相较原专题快照新增/明确了：

1. 没有 `permissions` block 时并非零权限，而是按模块注入一组默认只读 Permission；
2. 一旦声明 `permissions`，默认项不自动合并，Managed LLM Connector 需要显式 `ai_llm_gateway: access`；
3. Resource Key 会校验并丢弃未知项，Verb 不校验且错误时静默无权限；
4. Permission 在 CI/STO/SCS/IaCM 是 Stage 级，在 CD/Custom 是 Containerized Step Group 级，Token 对其中每个 Step 生效；
5. Trigger-initiated Run 仍无 scoped token，`scs_evidence_vault` 仍为 Beta/Flag。

这不是自治能力升级，而是把最小权限设计变得更具体，同时暴露了默认读权限、错误 Verb 静默失败和 Step Group Blast Radius 三个新的验收点。

### 4.2 Zero Trust Service 是新的可选 Runtime Oracle，不是 Worker 默认组成

Harness 在 [2026-07-17 工程文](https://www.harness.io/blog/building-a-zero-trust-service-for-ci-cd-how-we-intercept-every-task-before-it-executes) 披露客户侧 Zero Trust Service（ZTS）：Delegate 在执行每个 Task 前把完整 Task Payload 交给客户控制的 Validator Chain，得到二元 allow/deny；服务不可达时 fail closed。参考 Validator 包括 Account Allowlist、Shell AST Command Allowlist、Container Image Allowlist、Resolved Step Lookup 和 Custom Webhook，并独立记录审计。

这强化了“外部 Oracle/Policy 决定 Agent 能走多远”的观点，但公开材料没有把 ZTS 标成 Worker Agent 的默认 GA 组件；文章结尾要求联系 Account Team。因此它只能写为**新披露的客户控制参考架构 / 可选控制**，不能回写成所有 Worker Run 已经自动经过 ZTS。

### 4.3 AgentTrace 与 AI Evals 补上了 Agent 自身质量的 CI Oracle，但产品状态不同

- [AgentTrace，2026-07-21](https://www.harness.io/blog/introducing-agent-trace) 把 Run/Session 的 Model Call、Tool Call、Retrieval 和成本记录为 OTLP Trace，并把生产 Failure 导出为 Dataset，再回到 CI Regression Gate。开源的 [`harness-evals`](https://github.com/harness/harness-evals) 以显式 Threshold 评估 Correctness、Groundedness、Safety、Trajectory 和 Performance，可通过 `assert_test()` / Exit Code 让 CI 失败。开源 SDK/评测层已可用，但 Platform Agent 的 Edge Enforcement 被文章明确写为仍在构建。
- [Harness AI Evals，2026-07-21](https://www.harness.io/blog/introducing-ai-evals) 宣称提供 Native Pipeline Step、50+ Metric、Blocking/Advisory Eval Suite 和线上/离线闭环；同页 CTA 是“Request to start the beta”，产品页也是 “Join the Beta”。因此应标为 **Beta**，不能并入 GA Worker Agent。

它们是对现有 Oracle 清单的重要补充，尤其适用于评估 Agent/Prompt/Model 自身；但 LLM-as-judge 或概率 Metric 仍不是 Test、Scanner、OPA、Signature、SLO 和人工 Review 的替代品。

### 4.4 GA 标签没有消除受控开通

[Harness AI Overview，2026-07-22](https://developer.harness.io/docs/platform/harness-ai/overview/) 仍将 Harness Agents、DevOps Agent、Error Analyzer、Policy Generation 等列为 Generally Available；[Worker Agents，2026-07-20](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) 同时要求账户出现 AI Agents，缺失时联系 Admin/Support，并为 scoped Permission 保留 Feature Flag。故现有“三栏状态”框架继续有效：

1. **Capability Status：** 总览为 GA；
2. **Account Availability：** 账户入口、Support、Flag、Runtime 和 Connector 条件；
3. **Production Readiness：** Trigger 身份、默认权限、Tool Scope、Audit、Oracle、红队和成本已实测。

## 五、冲突与漂移

| 冲突 / 漂移 | 当前官方材料 A | 当前官方材料 B | 审计处理 |
|---|---|---|---|
| Token 绑定谁 | [Worker Agents 7/20](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)：`declared grant ∩ invoking principal RBAC` | [Agent permissions 7/15](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/)：Token 独立于 Pipeline Author 权限，取代 Author Credential/MCP 默认行为 | 视为阻断性文档冲突；在目标 Cluster 用 Manual/API/Service Account 分别验证 Subject、Scope、Expiry、Audit Principal |
| 支持资源范围 | Worker 7/20 列出 Core 与多模块 Resource Key/Default | Agent permissions 7/15 的 Limitations 又称仅 Pipeline 和 Code Repository，CI/CD/CCM/STO/SCS/IaCM 数据仍需用户配置 MCP | 不能按博客的“跨所有 Harness Module”做最低承诺；以实际 Token 调用矩阵验收 |
| 默认权限 | Worker 7/20 给出多模块默认只读列表 | 同页 Isolation 段又写 “No ambient permissions”；旧专题也按显式 Grant 理解 | 将“无 Ambient Permission”改成“无未声明写权限，但有文档化默认读权限”；显式配置并审计 Effective Permission |
| Trigger 支持 | Worker 支持所有标准 Trigger，并可注入 `<+trigger.*>` Context | 同页 Limitations 明确 Trigger Run 无 scoped token | 区分“能启动/能传 Context”和“有可归因 Principal/Token”；事件 Run 默认不开放写动作 |
| MCP Tool Intersection | 身份工程博客给出 Connector/Agent Allowlist 交集和 Per-call Attribution | Worker 产品文档未提供 `allowedTools` 字段或审计查询说明 | 保持 B 级架构 Claim；要求账户实测 Block、Log、Credential Injection 与参数脱敏 |
| Code Quality Runtime | Worker Marketplace 宣称 Managed、SLA、隔离 Runtime | Code Quality/PR Agent 文档仍展示 Run Step、专用 Execute API、PAT/LLM Key 和若干非 `harness/*`/`pkg.harness.io` 的 `latest` 镜像 | 视为独立实现；要求 Harness 说明 SKU、Owner、镜像签名/SBOM、Credential Path、版本和迁移关系 |
| AutoFix Loop | Managed Worker CI Autofix 会重触发 Build，循环至 Pass/max-turns | PR Agents/Code Quality 文档以生成、验证、Branch/PR 为主，示例没有同等清晰的重触发循环 | 页面必须注明“Marketplace Worker 版本”；不能把循环能力写成所有 AutoFix 实现共有 |
| AI Evals 状态 | 新产品/博客宣称 Native CI Gate 与 50+ Metrics | 同页 “Request to start the beta” / 产品页 “Join the Beta” | 标为 Beta；开源 `harness-evals` 与 Harness SaaS AI Evals 分开写 |

## 六、建议回流文件与精确修改点

本轮按任务约束不修改以下文件；建议主 Agent 后续按优先级回流。

### P0：权限事实与 Trigger 边界

1. `50_deepdives/harness-company/50_findings.md`
   - F6 中“Agent 没有常驻特权”后补一句：无 `permissions` block 时会注入文档化默认只读权限；声明 block 后默认不合并。
   - F6 增加警示：Stage/Containerized Step Group 的 scoped token 对组内每个 Step 生效，建议 Agent 单独分组。
   - 增加“7/20 Worker 页与 7/15 Agent permissions 页在 Principal 绑定和 Resource 覆盖上冲突”，把 `parent RBAC ∩ grant` 从无条件事实改为“最新 Worker 页声明、需账户验证”。
2. `50_deepdives/harness-company/20_evidence-map.md`
   - HAR-C13 由 `high` 下调为 `medium-high for intended model, low until account validation`，并加入两份权限文档冲突。
   - HAR-C14 保持 `medium-high`，明确 Tool Intersection 只有工程博客证据，缺产品字段/审计导出验证。
3. `50_deepdives/harness-company/research-evidence.md`
   - “默认 deny / no ambient permission”统一改为“默认无写权限，但有模块默认只读 Permission”。
   - 可用性表中 `Scoped Agent Permissions` 增加 Default Permission、`ai_llm_gateway: access`、Stage/Group Token 传播三列风险。

### P0：拆分 Code Quality 两套实现

4. `50_deepdives/harness-company/50_findings.md`
   - F2 专项 Agent 行补充：Code Repository/Code Quality 旧入口可能是 Run Step + Execute API/PAT；Marketplace Managed Agent 才可按 Worker Runtime 评估。
5. `50_deepdives/harness-company/90_report.md`
   - 3.3 Code Quality 行拆成“PR Agents/Execute API”与“Managed Worker Agents”两行。
   - 6 阶段表中的 AutoFix 描述改为：“PR Agent 版本生成/验证并建 PR；Marketplace CI Autofix 版本可重触发 Build 至 Pass/max-turns。”
   - 删除或收窄任何暗示三类 Code Quality Agent 都采用四层 Worker Runtime、Scoped Token 和 MCP Gateway 的文字。
6. `00_sources/briefs/2026-harness-code-quality-agents.md`
   - `availability: ga` 改为更保守的 `mixed`，正文区分 Code Repository Agent API、Code Quality Pipeline 示例和 Marketplace Worker。
   - 加入 Code Quality 示例镜像来源、`latest`、PAT/LLM Key、Go-only coverage verification 等供应链/覆盖限制。

### P1：补充最新 CI Oracle

7. `50_deepdives/harness-company/90_report.md`
   - 5.6 Oracle 增加 `Agent Eval`：开源 `harness-evals` 已可通过 Threshold/Exit Code 入 CI；Harness AI Evals SaaS 为 Beta；概率 Eval 不能替代传统 Oracle。
   - 5.4/5.6 增加 ZTS：客户控制、Task 执行前、Fail-closed 的可选验证层；状态写“工程披露/联系 Account Team”，不写 GA 默认能力。
8. `50_deepdives/harness-company/README.md`
   - `as_of` 更新时在关键结论增加：“7/20 权限文档存在冲突；7/21 AI Evals Beta 增加 Agent 质量 Gate，但未提高 Worker 自治证据等级。”

### P1：Presentation 精确修正

9. `80_presentations/agentic-cicd-company-workflows/slides/02-harness.md`
   - 保留主张：“Agent 进入受控 Pipeline，原始 Build/Test/Scan/Policy/Review 决定成功标准。”该主张通过复核。
   - 权限链脚注改为：“Manual/API Run 在 7/20 Worker 文档中按 Principal RBAC 与 Grant 求交集；事件 Trigger 暂无 scoped token；另有 7/15 权限页冲突，需账户验收。”
   - 不要把 Code Review/Coverage/AutoFix 画成共享同一 Worker Runtime 的固定三 Agent 链；可画为“专项 Agent/API 或 Managed Worker 模板”两种实现。
   - 若加入 AI Evals，必须标 `Beta as of 2026-07-22`；ZTS 标“工程披露 / 客户控制 / 非默认”。

## 七、自治等级与公开效果证据

| 场景 | 最可信等级 | 原因 |
|---|---|---|
| DevOps Agent 生成/修改 CI Pipeline、OPA 草案 | L2 | 用户 Review/Accept；OPA Save/Run 与原 Pipeline 权限仍是硬边界 |
| Code Review 评论/摘要 | L1—L2 | 输出是建议/评论；SCM Required Review 和人类 Merge 决定结果 |
| Coverage Agent 生成测试并建 PR | L2，沙箱内 L3 | 可修改代码和运行测试，但覆盖率不等于业务正确，最终 PR Review |
| AutoFix 在 PR Branch 修复并重跑 Build | L3（受限环境） | 可闭环到 Build Pass，但应冻结原 Gate、禁止改 Main/自动 Merge |
| 事件 Trigger 驱动写 Harness Resource | 暂不高于 L2 | 无 scoped Principal Token；身份、Connector 与归因未闭合 |
| Build/Test/Scan/OPA/Approval 后的批准动作 | 批准后 L3 | 动作由确定性 Oracle/Policy/人类 Gate 放行 |
| 无人值守 Merge、Artifact Promote、关键 Production Deploy | 不支持 L4 判断 | 无公开跨客户长期效果、安全审计和 Trigger 身份证据 |

截至 2026-07-22，公开效果证据没有本质改善：Worker Agent 仍主要是 United、Verint 的四天构建/推广引语；没有公开 Verified Fix Rate、False Fix、Reopen Rate、Regression Escape、平均 Turn/成本或长期 MTTR 对照。Harness QPE 团队对 AI Evals 的内部引语只能证明厂商内部采用方向，不能反向证明 Worker CI Autofix 已达到稳定生产自治。

## 八、Presentation-ready 判断

**结论：维持 `presentation_ready: true`，但属于“可汇报机制与边界”，不是“已证明大规模自治效果”。**

维持的理由：

- 核心页面主张由多份当前官方文档直接支持：Agent 是 Pipeline Step，Pipeline 继续承载编排、OPA、Approval 和外部 Oracle；
- DevOps Agent、Worker Agent、Code Quality 专项 Agent 的职责可以清晰拆分；
- GA、账户可获得性、生产就绪度的三栏框架仍能解释最新冲突；
- 最可信的 L2/L3 判断没有因新材料被推翻。

汇报前必须满足的条件：

1. 在页面中披露 7/15 与 7/20 权限文档冲突，不把 scoped Principal 写成任意 Trigger 的既成事实；
2. Code Quality 专项实现与 Marketplace Worker 实现分开画；
3. AI Evals 标 Beta，ZTS 标工程披露/非默认；
4. 效果页不得使用 90% Coverage Target、四天构建 Agent 或 GA 标签证明生产成功率；
5. 页面主张保持在“受治理 Pipeline 内的 Agent 执行”，不升级成“CI 已经自主闭环”。

## 九、来源清单

### 当前官方产品文档

- [Overview of Harness AI](https://developer.harness.io/docs/platform/harness-ai/overview/) — 2026-07-22；GA 总览与模型。
- [Worker Agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) — 2026-07-20；Stage、Runtime、Default Permission、Feature Flag、Trigger Limitations、MCP。
- [Agent permissions](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/) — 2026-07-15；Stage/Step Group Token、较窄 Resource/Identity 口径，用于识别冲突。
- [Harness Agents reference](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents-references/) — 2026-07-15；Instructions、Inputs、Environment、Agent Definition。
- [Agent examples](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/example-agents/) — 2026-07；Output Variables、MCP Context、Chained Agent 示例。
- [Harness AI DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/) — 2026-07-13；UI Agent、Pipeline/OPA/Resource、Error Analyzer。
- [Code Quality Agents](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/) — 2026-07-15；Review/Coverage/AutoFix 的 Pipeline 示例与输入。
- [Code Repository AI Agents](https://developer.harness.io/docs/code-repository/pull-requests/ai-agents/) — 2026-07-08；Execute API、PAT/Key、PR 交付。
- [Harness Policy As Code](https://developer.harness.io/docs/platform/governance/policy-as-code/harness-governance-overview/) — 2026-07-20；Save/Run/Step Start/Policy Step 与 Error/Exit。
- [Security Testing Orchestration](https://developer.harness.io/docs/security-testing-orchestration/) — Scan、Ingest、Failure Criteria 和 Policy。
- [Manual Harness Approval](https://developer.harness.io/docs/platform/approvals/adding-harness-approval-stages/) — 2026-06-09；CI Build/Test 之间人工 Gate。

### 官方工程、发布和代码

- [Introducing Autonomous Worker Agents](https://www.harness.io/blog/introducing-autonomous-worker-agents) — 2026-06-30；Marketplace、Managed Agent、CI Autofix Loop、编排。
- [Worker Agents GA Press Release](https://www.harness.io/press-and-news/harness-launches-autonomous-worker-agents-for-software-delivery) — 2026-06-30；GA 与客户引语。
- [How We Secured AI Worker Agents](https://www.harness.io/blog/how-we-secured-ai-worker-agents-in-harness) — 2026-07-13；四层隔离和第一方攻击回放。
- [Identity and Permissions for AI Worker Agents](https://www.harness.io/blog/identity-and-permissions-for-ai-worker-agents-in-harness) — 2026-07-16；委托 Principal、Tool Intersection、Per-call Attribution。
- [Building a Zero Trust Service for CI/CD](https://www.harness.io/blog/building-a-zero-trust-service-for-ci-cd-how-we-intercept-every-task-before-it-executes) — 2026-07-17；客户控制的 Task Pre-execution Validator。
- [Introducing Harness AgentTrace](https://www.harness.io/blog/introducing-agent-trace) — 2026-07-21；Trace、生产 Failure 到 CI Regression Gate、当前/未来边界。
- [Ship AI Agents You Can Trust](https://www.harness.io/blog/introducing-ai-evals) — 2026-07-21；Native Eval Step、Blocking/Advisory、Beta CTA。
- [harness/harness-evals](https://github.com/harness/harness-evals) — Apache-2.0；Threshold、`assert_test()`、OTEL/Langfuse 和 CI Exit。
