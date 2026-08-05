---
title: 两公司洞察页候选组合预研（2026-08-01）
tags:
  - research/agentic-cicd
  - research/topic-selection
status: pre-research
as_of: 2026-08-01
---

# 两公司洞察页候选组合预研（2026-08-01）

> [!warning] 选题预研，非 Source Brief，不得直接进入正式 Presentation
>
> 本文只用于选择下一项 Deep Dive 的范围。其链接是可复核的一手资料入口，但尚未完成逐主张事实审计、Deep Dive 必需交付物与 `presentation_ready` 门禁。所有来源访问日期均为 2026-08-01。

## 结论先行

**首选（更新）：AWS DevOps Agent + Datadog Bits Investigation / Bits Infrastructure Operations。**

推荐将一页写成：**“自愈进入生产后，关键不再是 Agent 能否给出修复建议，而是能否把‘证据—授权—执行—验证’变成可审计的状态机：AWS 以 operator-approved mutation 和 mitigation lifecycle 显式收紧云资源写权限；Datadog 以按资源/环境/动作范围的 guardrail，逐步从审批推进到自动修复。”**

这不是把两个产品并排罗列：AWS 的一手 IAM policy 明确将提升权限限定为 *operator-approved* 的 mutation，且其 EventBridge 事件模型含 mitigation 的开始、完成、失败、超时与取消；Datadog 的 Preview 明确将自动/审批修复绑定到可配置的 guardrail。两者共同把已有“CI 自愈”页从“失败后的修复闭环”推进到“生产环境中、可升级但有权限边界的自治闭环”。

## 候选排序

| 排名 | 公司组合 | 可成立的单页主张 | 相比已有五页的增量 | 证据强度与主要缺口 |
|---|---|---|---|---|
| 1 | AWS + Datadog | 生产自愈的成熟度由“证据—授权—执行—验证”状态机定义，而非是否使用 Agent。 | 直接把“CI 自愈”的有界写回原则推进至生产；同时承接基础设施页的策略、审批和回滚边界。 | 高；AWS 生产运维 GA 且 mutation/生命周期有正式文档，Datadog 生产自治相关能力为 Preview。不能暗示二者已互操作或同一套策略可移植。 |
| 2 | GitLab + AWS | 可信 Agentic Delivery 由“行动身份边界 + 生产验证事实”共同闭合。 | 在 GitHub/Harness 的作业编排、CI 自愈的修复闭环、基础设施与 CLI/MCP 之后，补上发布决策的授权—验证闭环。 | 高；GitLab Platform/Custom Flows 已 GA，AWS 生产运维 GA，但其 Release Management 是 `Preview` 且限 `us-east-1`。必须避免把预览能力写成普遍生产实践。 |
| 3 | JFrog + Snyk | Agent 接入供应链不能只“会扫描”：必须同时治理可接入的 MCP/Skill 资产，及单次诊断可用的工具面。 | 把已有 CLI/MCP 页从“接口选择”推进到“工具接入后的供应链安全与权限分层”。 | 中高；JFrog MCP Registry 为 GA；Snyk 文档确认本地 MCP 与 profile 限制，但产品级可用范围、审批和审计语义仍需专题核验。 |
| 4 | CircleCI + Buildkite | Agent 时代 CI 同时演化为低延迟内循环验证算力与原生受控 Agent 执行面。 | 将“CI 自愈”从修复逻辑推进到并发 Agent 的验证基础设施与运行模型。 | 中高；CircleCI 主要为工程实践与厂商自测；Buildkite 为正式产品文档但无发布日期与跨模型成本数据。与已有 CI 自愈页有部分重叠。 |
| 5 | Spacelift + AWS | 生产变更的 Agent 化有两条受治理路径：IaC 意图执行与基于运行拓扑的发布验证；两者都不能绕过 Policy、审批和最小权限。 | 把已有“基础设施”页提升为“基础设施变更与应用发布如何共用生产控制边界”。 | 中高；Spacelift 的 Available Now 为厂商自述，AWS 发布能力为 Preview；缺少跨云、复杂回滚的公开验证数据。 |

## 1. AWS DevOps Agent + Datadog Bits Investigation / Bits Infrastructure Operations（推荐）

### 为什么是这两家公司，而非分别介绍

事实：

- AWS DevOps Agent 的生产运维能力于 2026-03-30 GA；官方文档当前称它可自动进行根因分析、运行 remediation procedure 并配置预防措施。其 release management 是另一项 `Preview` 能力，不能混同为 GA。[AWS Document History](https://docs.aws.amazon.com/devopsagent/latest/userguide/document-history.html)；[AWS DevOps Agent 文档首页](https://docs.aws.amazon.com/devopsagent/)（GA：生产运维；访问：2026-08-01）
- `AIDevOpsAgentActionsPolicy` 创建于 2026-06-23，官方定义为供 DevOps Agent 在 operational event 中进行 **operator-approved mutations or elevated actions** 的提升权限。其 v1 列出 Auto Scaling、CloudFormation、ECS、Lambda、RDS 等可写 API；资源字段为 `*`，所以这不是最终的最小权限配置。[AWS Managed Policy Reference](https://docs.aws.amazon.com/aws-managed-policy/latest/reference/AIDevOpsAgentActionsPolicy.html)（创建：2026-06-23；状态：AWS managed policy；访问：2026-08-01）
- AWS 的 IAM 文档列出 `UpdateBacklogTask` 可用于批准 mitigation plan；EventBridge 文档对 mitigation 定义了 `In Progress`、`Completed`、`Failed`、`Timed Out`、`Cancelled` 生命周期事件，并能把 completed mitigation 接到后续流程。[AWS DevOps Agent IAM permissions](https://docs.aws.amazon.com/devopsagent/latest/userguide/aws-devops-agent-security-devops-agent-iam-permissions.html)；[AWS EventBridge integration](https://docs.aws.amazon.com/devopsagent/latest/userguide/configuring-integrations-and-knowledge-integrating-devops-agent-into-event-driven-applications-using-amazon-eventbridge-index.html)（发布日期：文档未标注；状态：正式文档能力；访问：2026-08-01）
- Datadog 的 Bits Investigation 文档定义其为端到端调查生产问题的 autonomous AI agent：迭代形成假设、收集 telemetry、以数据推理定位根因。[Datadog Bits Investigation 文档](https://docs.datadoghq.com/bits_ai/bits_investigation/)（发布日期/状态：文档未标注；访问：2026-08-01）
- Datadog 于 2026-06-09 发布 Bits Infrastructure Operations，明确处于 `Preview`。该能力按资源、环境、动作类型和 tag scope 定义 guardrail；在允许范围内自动修复，超出范围则准备 remediation plan，等待授权用户审批。官方示例包括直接修复 Kubernetes、同时开 IaC PR 以减少漂移。[Datadog 官方博客](https://www.datadoghq.com/blog/bits-infrastructure-operations/)（发布：2026-06-09；状态：Preview；访问：2026-08-01）

分析推断：两家公司都已越过“仅给修复建议”的阶段，但采取了不同且可比较的授权模型。AWS 把高风险写权限显式表述为 operator-approved，且把 mitigation 变成事件化、可观察的生命周期；Datadog 则使 guardrail 成为自治等级的调节器——同一类修复可在 staging 自动、在 production 要审批，并可在多次批准后由团队有意识地扩大自动化范围。单页真正的主张应是**“生产自愈不是开关，而是以证据与授权共同控制的升级阶梯”**。

建议版式：横向四段状态机 `告警/异常 → 证据化调查 → 按策略授权 → 修复与独立验证`；AWS 放在“授权 + 事件化执行回执”，Datadog 放在“telemetry 调查 + 分层 guardrail”。页脚直接标注 `AWS production operations: GA`、`Datadog Infrastructure Operations: Preview`。

必须保留的边界：不能宣称 AWS 与 Datadog 的 Agent 相互调用、共享策略或有端到端联合方案；AWS policy 的 `Resource: *` 提醒客户仍须自行设计更小的委托边界；Datadog 自动修复的可用范围与效果是厂商 Preview 说明，不能外推到所有故障类型或生产环境。

## 2. GitLab Duo Agent Platform + AWS DevOps Agent

### 为什么是这两家公司，而非分别介绍

事实：

- GitLab 的 Composite Identity 于 GitLab 18.8 GA。流/外部 Agent 在 Runner 上执行时，令牌合并发起用户与服务账号，实际权限取二者中更严格者，并保留人—Agent 的归因；CI job token 还有进一步范围限制。[GitLab Composite Identity 文档](https://docs.gitlab.com/user/duo_agent_platform/composite_identity/)（状态：GA；文档访问：2026-08-01）
- GitLab 19.2 的 Custom Flows 已 GA，支持在 GitLab CI/CD 中运行、多 Agent 编排、MR/Pipeline 等原生触发器、HITL checkpoint、服务账号与 Composite Identity。[GitLab 19.2 Release Notes](https://docs.gitlab.com/releases/19/gitlab-19-2-released/)（发布：2026-07；状态：GA；访问：2026-08-01）
- AWS DevOps Agent 的生产运维能力在 2026-03-30 GA；Release Management 在 2026-06-17 以 Preview 发布，仅限 `us-east-1`。该层将发布就绪评审与自主发布测试放在代码生成和生产部署之间，官方描述包括标准、跨代码库依赖、访问控制与基于环境的验证。[AWS 发布公告](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/)；[AWS Release Management 文档](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html)（状态：Preview；访问：2026-08-01）
- AWS 文档在 2026-06-11 增加了 GitLab MR 自动 release-readiness review 的配置项，包含 change-review toggle 与 runtime role configuration。[AWS DevOps Agent Document History](https://docs.aws.amazon.com/devopsagent/latest/userguide/document-history.html)（发布：2026-06-11；状态：随 Release Management Preview；访问：2026-08-01）

分析推断：GitLab 解决“Agent 以谁的权限、由谁负责而行动”，AWS Release Management 试图解决“此具体变更是否已用生产相关证据验证”。前者不能替代后者：可审计的最小权限并不证明发布安全；后者也不能替代前者：更好的运行分析不授予 Agent 合法行动权。因此它们能组成一张控制闭环图，而不是两个 Logo。

建议版式：左侧“GitLab：行动边界”（人 + 服务账号交集、Flow、MR/CI）；右侧“AWS：验证边界”（拓扑/依赖/运行环境 → readiness review/test）；中轴标明“发布决定 = 授权行动 × 独立验证”，底脚用醒目标签写明 AWS release-management 为 Preview。

必须保留的边界：GitLab 平台整体 GA 不代表 Orbit、治理 Agent 等所有周边能力均 GA；AWS Preview 不能外推为已验证的生产质量、覆盖率或跨区域能力。

## 3. JFrog + Snyk（安全与供应链备选）

### 可成立的单页主张

**“MCP/Skill 让 Agent 接入供应链后，安全控制从扫描代码扩展为：治理 Agent 可连接的工具资产，并约束每次调用暴露的诊断能力。”**

事实：

- JFrog 于 2026-03-18 宣布 MCP Registry GA，官方称其统一 MCP server、AI model 与 artifact 的治理，并支持工具级细粒度许可；同日 Agent Skills Registry 为 Beta。[JFrog Q1 2026 产品公告](https://jfrog.com/blog/q1-2026-new-innovations-announcement/)；[MCP Registry GA 公告](https://jfrog.com/blog/announcing-general-availability-of-the-jfrog-mcp-registry/)（发布：2026-03-18；状态：MCP Registry GA，Skills Registry Beta；访问：2026-08-01）
- Snyk 的官方文档说明其 MCP Server 是通过 Snyk CLI 运行的本地 MCP server，不提供托管 remote MCP server；并提供不同 tool profile 以控制向 AI Agent 暴露的工具。文档未给出明确 GA/Preview 标签。[Snyk Studio 文档](https://docs.snyk.io/integrations/snyk-studio-agentic-integrations/getting-started-with-snyk-studio)（状态：文档未标注；访问：2026-08-01）

分析推断：JFrog 代表企业级“接入什么工具资产”的准入与系统记录，Snyk 代表“本次 Agent 可执行哪些安全诊断工具”的执行面缩减。二者都不是通用 Agent 编排器，因而能补齐而非重复 GitHub/Harness。

缺口：需核验 Snyk 的版本/套餐可用性、profile 是否能覆盖写操作，及两者在同一企业中的联合实施案例；没有这些，不能宣称端到端零信任供应链已实现。

## 4. CircleCI + Buildkite（CI 基础设施备选）

### 可成立的单页主张

**“高自主 Agent 不应挤占最终 CI：CircleCI 将验证拆成低延迟内循环与完整外循环；Buildkite 则把 Agent 既做成 CI 的使用者，也做成 Pipeline 中受控执行的 Step。”**

事实：

- CircleCI 介绍 Chunk sidecars/microbuilds：预热环境、增量 patch 同步、直接向 Agent 返回精简验证结果；其工程文章报告内部测得结果相对直接消费 CI logs 更 token-efficient、相对完整 CI pipeline 更 core-cost-effective。[CircleCI 工程博客](https://circleci.com/blog/agentic-validation-needs-different-infrastructure/)（发布：2026-06-05；状态：工程方法/厂商自测，不是独立 GA 产品声明；访问：2026-08-01）
- CircleCI 说明 Chunk sidecars 于 2026-05-22 面向所有计划（包括 Free）可用。[CircleCI 产品博客](https://circleci.com/blog/chunk-sidecars/)（最后更新：2026-05-22；状态：Available；访问：2026-08-01）
- Buildkite 正式文档说明 Agent 可使用 Skills/MCP 读取 build state、日志和触发运行，也可在 Pipeline Step 中通过 model providers 使用日志、artifact、安全策略与实时 pipeline data；文档将二者称为互补路径。[Buildkite 文档](https://buildkite.com/docs/platform/ai-agents)（发布日期：未标注；状态：正式文档能力；访问：2026-08-01）

分析推断：CircleCI 回答“怎样用经济的验证反馈让 Agent 多轮工作”，Buildkite 回答“怎样把 Agent 纳入已有 Pipeline 的受控执行面”。这是运行架构比较，不是模型能力比较。

缺口：CircleCI 的量化为厂商自测、Buildkite 未给跨模型/权限/成本矩阵；且与现有“CI 自愈”页共享失败—复验叙事，应只有在该页聚焦修复闭环而非运行基础设施时才选择。

## 5. Spacelift + AWS（基础设施—发布备选）

### 可成立的单页主张

**“Agent 不只生成 IaC：Spacelift 允许意图直接进入既有 Policy/Approval 控制面；AWS 则用拓扑与运行环境把发布验证拉近生产。两条路径的共同前提是控制面不因自然语言入口而失效。”**

事实：

- Spacelift Intelligence 于 2026-03-18 发布，包含能理解 stack/state/run/configuration 的 Infrastructure Assistant，以及自然语言部署的 Intent；官方称既有 IaC、GitOps、VCS 集成、policy 与 approval 保持不变，产品“Available Now”。[Spacelift 官方博客](https://spacelift.io/blog/introducing-spacelift-intelligence)（发布：2026-03-18；状态：厂商称 Available Now；访问：2026-08-01）
- AWS Release Management 的事实和 Preview 边界同上。[AWS 发布公告](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/)（发布：2026-06-17；状态：Preview；访问：2026-08-01）

分析推断：Spacelift 是“基础设施改变如何合法发生”的控制面样本；AWS 是“应用改变何时适合进入生产”的验证面样本。二者配对适合 CTO/平台工程受众，但比首选更依赖对 IaC 与应用发布关系的解释。

缺口：缺少自然语言基础设施变更的回滚、漂移与复杂故障公开结果；AWS 的区域和 Preview 限制仍在。

## 选题决策规则

- 若要把已有**CI 自愈**提升到最有决策价值的生产控制议题，选择 **AWS + Datadog**；标题避免“全自动运维”，应强调“证据与授权驱动的自治升级阶梯”。
- 若这页要作为现有五页后的**交付闭环收束页**，选择 **GitLab + AWS**；标题避免用“端到端自治”，应强调“授权与验证闭环”。
- 若要回应安全/治理负责人最关心的 MCP 风险，选择 **JFrog + Snyk**；它最能承接 CLI/MCP 页。
- 若要扩展 CI 自愈的底层工程问题，选择 **CircleCI + Buildkite**；但先确认不与现有自愈页重复。
- 无论选择哪组，先建立对应两公司 Deep Dive 或一个明确的跨公司 Deep Dive，并在事实审计后才允许 `presentation_ready: true`。
