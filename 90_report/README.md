---
title: Agent 技术在 CI/CD 中的应用与实践洞察主报告
aliases:
  - Agentic CI/CD Insight 2026
tags:
  - research/agentic-cicd
  - report
status: complete
workflow: batch-insight
report_role: final-viewpoint-output
as_of: 2026-08-08
audience:
  - CTO
  - 研发效能负责人
  - 平台工程负责人
---

# Agent 技术在 CI/CD 中的应用与实践洞察

> [!info] 文档角色
> 本文是批量洞察的最终观点和决策输出。[[90_report/seven-dimension-analysis|七维交叉分析]]负责组织维度、检验候选观点和处理交叉关系；专题报告提供单对象深度证据。企业建议、路线与最终观点只在本文维护。

**观察窗口：** 2025-07-01—2026-08-08，重点关注 2026 年
**趋势展望：** 2027—2028 年
**研究范围：** 编码完成后的代码评审、安全与测试、构建出包、制品与版本、环境与部署、发布、发布后验证与恢复
**证据基础：** 81 条核心一手资料，78 个深度 Source Brief；商业平台、大型公司内部实践、高影响力开源项目、原始研究与治理资料

> [!abstract] 一句话结论
> Agent 不会在未来两年替代 CI/CD；它会成为覆盖现有 CI/CD 之上的“推理与行动层”。确定性的流水线、测试、策略、制品和发布控制继续充当执行骨架与安全 Oracle，竞争重心转向上下文、受控工具、身份授权、反馈速度和可证明结果。

### 观点校准

以下置信度与边界均以本文观察窗口和 `as_of: 2026-08-08` 为准。

| 最终观点 | 置信度 | 关键反例或不能外推的边界 |
|---|---|---|
| 已从 AI 建议进入 Agent 执行，但未到全流程自治 | 高 | 产品 GA、单次演示或 L3 行动权都不等于端到端任务成熟 |
| 可信架构是动态推理与确定性执行的双层系统 | 高 | Agent 不能同时修改成功标准、批准动作并解释结果 |
| 上下文与控制面比单一模型更能形成平台壁垒 | 中高 | 上下文必须满足权限、新鲜度和结构化要求，拥有数据不自动产生可靠 Agent |
| 评审、CI 修复和只读调查领先，关键发布自治最慢 | 中高 | 厂商案例不能外推为行业自愈率，生产 L4 证据仍稀少 |
| 人的工作转向意图、证据、风险和异常管理 | 中高 | 角色变化受组织成熟度影响，不能从少数大型公司直接外推 |
| 身份、边界和独立验证是获得执行权的首要治理条件 | 高 | 现有企业架构和草案标准证明方向可行，不代表互操作标准已经稳定 |
| 度量必须从生成量转向系统效果和单位成功成本 | 高 | 现有研究多为相关性或第一方数据，落地前仍需企业自身基线 |

## 一、给决策者的七条结论

### 1. 2026 年已经从“AI 建议”进入“Agent 执行”，但不是全流程自治

最明确的变化是输出形态：从评论、摘要和问答，转为修复 PR、测试、Pipeline Patch、Policy、Release Readiness Review、Runbook 和经批准的运维动作。GitHub Agentic Workflows 将自然语言 Markdown 编译为标准 Actions，并使用只读默认、沙箱和 safe outputs 约束写入；到 2026-06 已从技术预览进入公开预览。[GitHub 官方公告](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview/)

其实现比“Markdown 写 Workflow”更完整：Compiler 将 Frontmatter 约束转成 Pre-activation、Agent、Threat Detection 和 Safe Output 等独立 Actions Job；Agent 只生成 JSON/Patch Artifact，写 Token 只在受信的后置 Job 中出现。复杂任务通过 DeterministicOps、Orchestrator/Worker 和 CentralRepoOps 拆分，说明 Agentic CI/CD 正从单 Agent Step 走向“编译计划 + 权限阶段 + 类型化输出 + 外部 Oracle”。但该产品仍为 Public Preview，版本、成本和兼容性必须持续运营。详见 [[50_deepdives/github-agentic-workflows/90_report|GitHub Agentic Workflows 深度报告]]。

GitLab Duo Agent Platform 已在 18.8 宣布 GA，公开 Flow 覆盖 Code Review、Fix CI/CD 和 SAST 修复；Harness 则把 DevOps Agent、Pipeline 内 Worker Agents、Knowledge Graph/HQL、MCP/CLI/Skills 及 Test/Security/SRE 专项 Agent 组合成软件交付控制面；AWS DevOps Agent 的生产运维能力已 GA，而发布管理仍是 Preview。[GitLab GA 公告](https://about.gitlab.com/press/releases/2026-01-15-gitlab-announces-duo-agent-platform-general-availability/)；[Harness AI 总览](https://developer.harness.io/docs/platform/harness-ai/overview/)；[AWS GA 公告](https://aws.amazon.com/about-aws/whats-new/2026/03/aws-devops-agent-generally-available/)

因此，行业已跨过 L0/L1 演示期，但当前主流生产边界仍是 L2 可审查变更和 L3 批准后执行。跨关键生产发布的普遍 L4 没有足够证据。

### 2. Agentic CI/CD 的核心架构是“双层系统”，不是动态流程取代确定性流程

~~~mermaid
flowchart TB
  subgraph D["Agent 决策与行动层"]
    C["上下文：代码/历史/拓扑/策略"] --> P["规划与工具选择"]
    P --> T["受控 Tool / MCP / Skill"]
  end
  subgraph E["确定性 CI/CD 执行与证明层"]
    R["Repo / PR"] --> CI["Build / Test / Scan"]
    CI --> A["Artifact / Signature / Policy"]
    A --> DEP["Deploy / Release"]
    DEP --> O["SLO / Observe / Rollback"]
  end
  T --> R
  T --> CI
  T --> DEP
  CI --> C
  A --> C
  O --> C
  H["Human intent / approval / accountability"] --> P
  H --> DEP
~~~

Agent 擅长开放式理解、假设生成和多步骤调查；编译器、测试、扫描器、Policy-as-Code、签名和 SLO 擅长确定性验证。成熟产品都在组合两者：Snyk 用安全引擎复验 Agent 修复，Tricentis 用确定性测试引擎执行生成用例，Spacelift 让自然语言部署沿用相同 Policy/Approval，GitHub 将 Agent 意图编译进 Actions。

企业架构原则应是：**模型负责提出下一步，外部系统负责定义是否成功。**

### 3. 上下文控制面将取代单一模型，成为平台竞争的主要壁垒

代码评审需要仓库、历史规则和服务上下文；CI 修复需要日志、构建历史、测试和 Runner 环境；发布需要跨仓依赖、制品、审批和事故历史；生产调查需要拓扑、遥测、部署和代码。

这解释了不同公司的入口：GitHub/GitLab 以代码与 DevSecOps 数据面切入，AWS/Datadog 等以云拓扑和遥测切入，Atlassian 以工作项和知识切入，JFrog/Snyk/Tricentis 以供应链、安全和测试事实切入。2026-06，Microsoft 公开的内部迁移案例显示，其 CAP 组织在 6 个月内迁移超过 1,600 个仓库和 3,100 名开发者以获得 GitHub 的 Agentic 能力，同时仍可保留 Azure Boards/Pipelines，反映大型企业会为 Agent 上下文调整平台边界，而不是立即替换全部 ALM。[Microsoft 工程博客](https://devblogs.microsoft.com/devops/how-microsoft-is-migrating-repositories-to-github/)

未来两年，模型能力会快速商品化，能够安全连接企业上下文、策略和动作的控制面更难复制。

Harness 的实现把该趋势写得很清楚：已建模的 Read/Query/Analyze 优先使用 Software Delivery Knowledge Graph/HQL，长尾工具和写动作使用 MCP，Pipeline 负责确定性编排与 Gate。上下文、动作和执行并非同一层；把所有 API 暴露成 Tool 反而会增加选择、Join、权限和 Token 成本。完整架构见 [[50_deepdives/harness-company/90_report|Harness 公司深度报告]]。

CLI-Anything 进一步显示，竞争面不只在“谁拥有更多现成 MCP”：缺少 Agent 接口的遗留和内部软件，也可以从源码/API 生成带测试、结构化输出和 Skill 的 CLI。它降低了长尾工具进入 Tool Plane 的门槛，但生成接口仍需逐项评审、签名、授权和沙箱运行，不能把接口可调用等同于生产可执行。参考 [[00_sources/briefs/2026-cli-anything|CLI-Anything Brief]]。

### 4. 最先成熟的是评审、CI 自愈和事故调查；最慢的是制品、版本与关键发布自治

成熟度与三个条件正相关：上下文是否结构化、结果是否能被确定性验证、动作是否可逆且可隔离。PR 同时满足三者，所以代码评审和修复最成熟；CI 失败可由重跑验证，因而自愈进展很快；生产调查上下文丰富但动作风险大，所以“分析 GA、自治 Preview”很常见。

但“自愈”必须拆成 SH0 感知、SH1 诊断、SH2 候选修复、SH3 隔离验证与受限写回、SH4 自动观察和回退。GitHub CI Doctor、GitLab Fix Pipeline 和 CircleCI Chunk 的主流边界是 SH1—SH2；Nx 和 Harness 已把失败任务复验、限次循环或 PR 分支写回做成 SH3，Nx 的白名单 Auto-apply 在 PR 微域可到局部 SH4。AWS DevOps Agent 虽能自动调查并生成 Mitigation Plan，但官方明确不代操作员执行 Remediation，不能归为生产 L4。完整分级、快慢双环和企业实施方法见 [[50_deepdives/cicd-self-healing/90_report|CI/CD 问题自愈深度报告]]。

[CI-Repair-Bench](https://arxiv.org/abs/2604.27148) 又提供了必要校准：567 个真实 CI 故障中，最佳受测模型仅修复 18.9%。因此企业不应追求一个“通用自愈率”，而应按 Code、Flaky、Transient、Runner/Cache、Dependency/Config 和 Unknown 分类授权、分别评测。

Harness 的公开效果也需要同样校准：通用 Worker Agent 的大型企业材料主要证明 4 天构建专项 Agent 和组织推广意向；较具体的量化数据来自 AI Test Automation 的三个第一方案例，不能外推成 Worker Agent 或整个 CI/CD 的平均自愈率。

制品仓和发布平台的 Agent 行动面已经出现：Cloudsmith MCP 可查询漏洞、列举版本和管理制品；GitHub 可把 Dependabot 告警交给 Coding Agent 生成修复 Draft PR；Octopus 已把 Claude Agent 做成原生部署 Step；Terraform MCP 明确区分 Plan、批准后 Apply 与显式开放的自动批准/销毁。[Cloudsmith 官方更新](https://cloudsmith.com/changelog/manage-your-supply-chain-using-natural-language-with-mcp)；[GitHub 官方更新](https://github.blog/changelog/2026-04-07-dependabot-alerts-are-now-assignable-to-ai-agents-for-remediation/)；[Terraform MCP 参考](https://developer.hashicorp.com/terraform/mcp-server/reference)

但“有行动接口”不等于“有发布授权”。Octopus Agent Step 仍为 Alpha，AWS Release Management 到 2026-06 仍是 Preview，签名、跨环境晋级、自动批准和关键生产发布仍缺少可信 L4 证据。[AWS Release Management 公告](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/)

### 5. 人的工作没有消失，而是从“执行步骤”迁移到“定义意图、验证证据、控制风险和处理异常”

Agent 让开发者减少查日志、重复修配置和机械评审，但增加了写清意图与验收条件、审查 Agent 计划、判断业务语义和承担最终责任的要求。平台团队从模板与门户建设者转为 Agent Runtime、Context/Tool Plane、权限、预算和评测的运营者；QA 转向测试 Oracle 与风险模型；安全团队新增 Agent 身份、委托链、工具供应链和提示注入治理；SRE 转向 Runbook、SLO 和恢复边界设计。

Agent 工作台让这条变化线更具体。WorkBuddy、ChatGPT Work 和 Codex 已经能够证明开发者/最终用户可从统一工作面提交复杂目标、补充项目上下文、观察专家或 Subagent 并审查候选产物；WorkBuddy Enterprise 与 ChatGPT Workspace Agents 则证明专家、Skill、App、版本、分享、发布和成员权限可以在后台由管理员或 Builder 经营。由此形成新的候选观点：

> **交付能力正在前台化给开发者，发布与运维能力则在后台沉淀为 Skill、专家团与治理规则。**

这里的“正在”是跨产品机制形成的方向判断，不是跨企业岗位事实。目标 operating model 是三层分权：开发者工作台消费交付能力，通用 Agent Harness 供应和运营专家体系，确定性 CI/CD 控制面继续用 Test、Scan、Policy、Signature、Approval、SLO 与 Rollback 接受或拒绝候选动作。发布、运维、SRE 和平台人员的价值重心可从逐单执行上移到 Harness 设计、评测、权限、异常升级和生命周期治理；尚不能据此声称岗位消失、职责迁移已经普及或开发者能够绕过生产责任分离。详见 [[50_deepdives/agent-workbench/90_report|Agent 工作台、专家团与交付角色重构专题报告]]。

对 278,790 条评审对话的 2026 年研究发现，人类更常补充上下文、测试和知识传递，Agent 建议也存在较高的不采纳与错误/替代修复比例，说明“人类负责高语境判断”仍是必要分工。[原始论文](https://arxiv.org/abs/2603.15911)

### 6. 当前最大风险不是模型不够聪明，而是 Agent 获得执行权后缺少身份、边界和独立验证

传统机器人账号通常是共享、长期和面向固定脚本；Agent 会动态选择工具并处理不可信代码、Issue、日志和网页，因此风险包括提示注入、工具投毒、越权、共享身份、无限重试、为通过 Gate 而弱化测试，以及错误在多仓和多环境快速扩散。

NIST NCCoE 2026-02 的概念稿已将 Agent 身份、授权、审计、不可否认性和提示注入列为企业采用问题。更具体的生产信号来自 Uber：其 Agent Registry、SPIFFE/SPIRE、单跳短期 JWT 和 MCP Gateway 保留完整的人→Agent→Agent→工具委托链，并被数千个内部 Agent 使用；这是第一方数据，不能外推，但证明该架构可落地。[NIST 官方页面](https://csrc.nist.gov/pubs/other/2026/02/05/accelerating-the-adoption-of-software-and-ai-agent/ipd)；[Uber 工程实践](https://www.uber.com/au/en/blog/solving-the-agent-identity-crisis/)

OpenID AuthZEN 的 AARP/COAZ 已把批准、委托、风险证明和 MCP Tool 授权写成 Working Group Draft；它们不是最终标准，却明确了方向：Agent 被拒绝后应补齐前置条件并由外部 Policy 重新决策，而不是在 Prompt 中绕过限制。[OpenID Foundation](https://openid.net/openid-foundation-advances-authorization-for-the-agent-era-with-new-authzen-working-group-drafts/)

Harness 2026-07 公布的 Worker Agent Runtime 提供了一个产品化对照：把 Agent 视为已被攻陷，使用硬化 Image、Agent/Broker/Egress 三用户隔离、Host-bound Secret Placeholder 和默认拒绝网络；存在触发 Principal 时，身份层让单次 Token 只获得该 Principal 权限与声明 Grant 的交集，MCP Tool 再做 Allowlist 交集并逐次归因。这些控制仍是第一方声明，细粒度权限还需验证目标账户 Feature Flag；Webhook/Schedule/Artifact/Manifest 等 Trigger Run 当前也不能继承触发人 scoped token，必须另建身份与审批模型。整体上仍比共享 Bot Token 或仅靠 Prompt Approval 更接近生产基线。参考 [[00_sources/briefs/2026-harness-worker-agent-security|Harness Worker Agent 安全与身份]]。

近期治理基线应是：默认只读、每任务独立身份和短期凭据、少数受控写出口、职责分离、完整审计、预算/轮次上限、可停止可回退，以及在企业真实任务上的持续评测。

### 7. 衡量 Agentic CI/CD 必须从“生成量”转向“系统效果”

DORA 2025 基于近 5,000 名技术人员和 100 多小时定性材料，发现 AI 使用与吞吐和产品表现正相关，但仍与交付稳定性负相关；其结论强调 AI 会放大现有工程系统，高质量平台、测试、版本控制和快速反馈是收益条件。[DORA 报告](https://dora.dev/research/2025/dora-report/)

真实 Agent 会话研究 SWE-chat 也提示，Agent 生成量不等于最终价值：初始数据中只有 44% Agent 生成代码保留到提交，44% 轮次出现纠正、失败反馈或中断。[SWE-chat](https://arxiv.org/abs/2604.20779)

企业应把任务成功、长期回归、人工介入、错误修复、缺陷逃逸、变更失败、审计完整和每成功任务成本放在一起，而不是只追踪使用率或代码行数。

## 二、沿八个 CI/CD 阶段的影响判断

| 阶段 | 当前主流应用 | 产品/实践信号 | 推荐自治上限 | 未来两年判断 |
|---|---|---|---|---|
| 1. 代码评审与质量 | 仓库级评审、专业 Reviewer、修复 PR | GitHub、GitLab、Harness、Qodo、Atlassian、云效 | L2 | 成为默认前置筛查，人聚焦高语境判断 |
| 2. 静态/安全/依赖/合规 | 发现解释、根因、修复与复验 | Snyk、Sonar、Semgrep、GitLab、Harness | L2，局部 L3 | Analyzer + Agent 成为标准架构 |
| 3. 测试/门禁/风险 | 测试生成/选择、Coverage、动态验证 | Harness、Tricentis、CircleCI | L3 仅限沙箱验证 | CI 计算与反馈时延成为 Agent 瓶颈 |
| 4. 编译/构建/出包 | 分类、日志诊断、配置修复、原任务复验与有界重跑 | CircleCI、Harness、GitHub、GitLab、Nx | L2—L3；PR 微域局部 L4 | 高频反馈推动 Runner/缓存重构，Oracle 必须外置 |
| 5. 制品/供应链/版本 | 可信包/策略查询、版本修复、有限制品操作 | JFrog、Cloudsmith、Sonatype、GitHub、Harness | L2 | 行动面已出现，晋级和签名仍保守 |
| 6. 环境/IaC/部署 | 意图转 Plan、策略解释、原生 Agent Step、受控部署 | Terraform MCP、Octopus、Spacelift、Harness、Akuity | L3 | 自然语言成第二入口，但不绕过 Policy |
| 7. 发布/审批/变更 | 就绪评审、依赖分析、变更冲突/窗口、批准后执行 | AWS、Harness、Octopus、ServiceNow | L3 | 受限执行增加，通用 L4 仍少 |
| 8. 发布后验证/恢复 | 跨源调查、根因、Runbook、受限恢复 | AWS、Harness、Azure、Datadog、CloudQ、HolmesGPT、Akuity | 分析 L1—L2；批准动作 L3，局部 L4 | 分析快速普及，低风险恢复有限闭环 |

完整阶段分析见 [[30_summaries/stages/README|八阶段维度总结]]。

## 三、平台、流程、人员三条变化线

### 工具平台

| 从 | 到 |
|---|---|
| 各工具为人提供 UI | 各平台同时提供 Agent 可调用 Tool/MCP/Skill |
| 长尾软件依赖 GUI 或隐式操作知识 | 从源码/API 生成或手工建设可测试 CLI/Skill，再经过准入进入 Tool Catalog |
| Pipeline 执行固定步骤 | Pipeline 同时承载 Agent 的隔离执行与反馈循环 |
| 日志主要供人阅读 | 日志、历史、拓扑和证据结构化供 Agent 查询 |
| 共享机器人账号 | Agent/任务身份、短期凭据和委托链 |
| 只治理代码与制品 | 同时治理模型、Prompt、Skill、MCP 和 Agent 资产 |
| 只按构建次数计成本 | 按成功任务、重试、Token、Runner 和风险计成本 |

### 工作流程

| 从 | 到 |
|---|---|
| 固定 DAG 决定全部步骤 | Agent 动态计划，确定性 Gate 决定能否继续 |
| 失败后人跨工具排查 | Agent 聚合上下文并多轮验证，人处理异常 |
| 审批一个 Pipeline 运行 | 审批带制品、环境、证据和有效期的具体计划 |
| 每次变更同样验证 | 按风险和上下文动态选择验证深度 |
| 自动化脚本失败后停止 | Agent 在预算内修复/重试，超界升级人工 |

### 人员能力

| 角色 | 能力重心变化 |
|---|---|
| 开发者 | 意图、验收条件、证据审查、业务语义、异常接管 |
| 平台/研发效能 | Context/Tool Plane、Runtime、权限、评测、预算和自治运营 |
| QA | 风险模型、测试 Oracle、变异/未来测试、Agent 评测集 |
| 安全/IAM | Agent 身份、委托链、工具供应链、外部策略和红队 |
| SRE/发布 | SLO、Runbook、渐进策略、恢复边界和事件指挥 |
| 管理者 | 系统效果、责任设计、平台瓶颈和风险收益组合 |

完整横向分析见 [[40_summaries/crosscutting/README|横向变化总结]]。

## 四、主要公司战略分化

### 全生命周期平台：争夺 Agent 控制面

- **GitHub**：Agentic Workflow 编译到 Actions；代码、PR、Actions、安全和 MCP 形成统一入口。
- **GitLab**：Duo Agent Platform GA，依靠 DevSecOps 全生命周期上下文、Self-Managed 和治理能力。
- **Harness**：从 Pipeline 产品扩展为 Agent 控制面；DevOps Agent 设计/操作平台，Worker Agent 在 Pipeline 内执行，Knowledge Graph/HQL 与 MCP 分别承担结构化理解和长尾行动，RBAC/OPA/Approval/Scoped Token/Audit 限定自治。详见 [[50_deepdives/harness-company/90_report|公司专题]]。
- **Microsoft/Azure DevOps**：GitHub 承担新 Agent 能力，ADO 通过 Boards/Pipelines/MCP 混合保留。
- **Octopus Deploy**：以 GitHub Agent App、MCP 和原生 Claude Agent Step 将部署状态、Runbook、发布步骤和审计连起来，但关键 Step 仍为 Alpha。

### 云与可观测平台：从运行态反向进入发布

- **AWS**：生产运维 GA，发布管理 Preview；以云拓扑、遥测、部署和身份为优势。
- **Google Cloud**：Gemini Cloud Assist 覆盖应用生命周期，DORA 强调平台质量和反馈系统。
- **Azure、Datadog、CloudQ、HolmesGPT**：先强化调查和证据组织，再逐步开放受限动作。

### 专业工具：提供 Agent 不应自行制造的 Oracle

- **Snyk/Semgrep/Sonar**：安全与质量检测事实。
- **Tricentis**：可执行测试资产与确定性测试引擎。
- **JFrog/Cloudsmith/Sonatype**：可信包、制品、漏洞、许可证、版本情报和受控制品动作。
- **Spacelift/Akuity**：IaC/GitOps 状态、Policy 与部署动作。
- **ServiceNow**：受影响服务、变更冲突、窗口与企业 Change Request。

### 中国厂商：评审与 CloudOps 较明确，端到端证据有限

云效的 MR CodeReview 与组织规则、腾讯 CloudQ 的 ChatOps/AIOps/CloudOps 是较清晰产品样本；美团的 31 万行系统 Pre-PR 实践和京东 JoyAgent 双 RAG 评审补充了内部流程与上下文工程证据。百度 Comate 和华为 CodeArts 公开能力更多集中在研发 Agent 与平台助手。不能从“厂商拥有完整 DevOps 产品线”推断“Agent 已打通端到端 CI/CD”。中国企业还需提高私有化、数据边界、本地 IAM/IM 和审计留存的权重。

完整公司分析见 [[20_summaries/companies/README|公司维度总结]]。

## 五、企业参考架构

~~~mermaid
flowchart TB
  U["用户/事件/计划任务"] --> CP["Agent Control Plane"]
  CP --> ID["任务身份与委托"]
  CP --> EV["模型/Skill/Agent 版本与评测"]
  CP --> BG["预算、轮次、时限、风险等级"]
  CP --> RT["Agent Runtime / Sandbox"]

  RT --> CTX["Context Plane"]
  CTX --> C1["Repo/PR/Work Item"]
  CTX --> C2["Pipeline/Test/Security"]
  CTX --> C3["Artifact/SBOM/Policy"]
  CTX --> C4["Topology/Telemetry/Incident"]

  IF["Interface Factory<br/>Source/API → CLI/Test/Skill"] --> GW
  RT --> GW["Tool Gateway / MCP / Skills"]
  GW --> RO["只读调查工具"]
  GW --> WR["受控写工具"]
  WR --> AP["PR / Approval / Safe Output / Runbook"]
  AP --> EX["确定性 CI/CD 执行层"]
  EX --> OR["Test/Scan/Policy/Signature/SLO Oracle"]
  OR --> AUD["不可变证据与审计"]
  AUD --> EV
~~~

### 架构底线

1. 只读调查工具与生产写工具分开注册和授权。
2. Agent 不直接持有长期生产密钥；任务身份与委托人绑定。
3. 所有写动作经过 PR、Approval、Safe Output 或预批准 Runbook。
4. Agent 不能同时修改 Gate 规则并用该规则证明自己的变更。
5. 模型、Skill、MCP、工具版本、上下文和结果都进入审计。
6. 每个长任务有 Token、Runner、时间、重试和动作预算。
7. 自治失败时能停止、降级为只读、回退或交给人。

## 六、建议的 18 个月落地路线

### 0—3 个月：建立基线并选择低风险场景

- 建立 Agent 资产和现有使用清单，禁止共享高权限机器人账号。
- 选择 2—3 个高频、可回滚、可验证场景：PR 评审、CI 失败归因/修复、只读事故调查。
- 统一输出为评论、Draft PR 或证据包，不自动合并和生产写入。
- 建立企业任务评测集和基线：成功、错误、人工介入、Lead Time、成本。
- 记录模型、Agent/Skill 版本、工具调用、批准和结果。

**退出条件：** 能回答“Agent 做了什么、为何这样做、谁委托、依据什么、效果如何”。

### 3—6 个月：形成可验证执行闭环

- 建立受控 Tool/MCP 目录、沙箱和短期任务身份。
- 将安全扫描、测试、Policy 和构建结果作为 Agent 外部 Oracle。
- 开放 L2 修复 PR 和非生产 L3 重跑/环境操作。
- 设置预算、最大迭代、失败升级和 Kill Switch。
- 在至少一个业务域验证 Agent 对 DORA 类系统指标的净影响，而非只看使用量。

**退出条件：** 在选定任务上可证明质量不劣化、审计完整、失败可控、经济性可接受。

### 6—12 个月：平台化与风险分级

- 建设统一 Context Plane，把代码、Pipeline、制品、拓扑和策略按权限连接。
- 依据任务类型、环境、Blast Radius 和评测成绩动态授权 L1—L3。
- 在 IaC、GitOps、发布就绪和受限 Runbook 中扩展批准后执行。
- 加入在线监控、真实失败回放、红队和 Agent/Monitor 升级机制。
- 明确平台、QA、安全、SRE 和业务负责人的职责边界。

**退出条件：** Agent 不再是各团队散装工具，而是有统一身份、工具、评测和成本运营的平台能力。

### 12—18 个月：只在可证明场景开放 L4

- 选择可逆、低爆炸半径、成功条件清晰、已有可靠 Runbook 的任务。
- 使用渐进式范围、自动停止、确定性回滚和人工随时接管。
- 对制品、发布和生产动作保持职责分离及更高证据门槛。
- 持续按模型/Agent 版本重新验证，性能下降自动降权。

**退出条件：** L4 是按场景证明的能力，而不是按产品或模型授予的永久身份。

## 七、采购和自建的决策清单

### 上下文

- 是否能访问代码、Pipeline、测试、制品、策略、拓扑和事故上下文？
- 数据是否实时、结构化、可追踪，并遵守原有权限？
- 是否支持组织自有规范、Skill、知识库和服务目录？
- 对缺少稳定 API/CLI 的内部工具，是否支持生成或建设机器接口，并保留来源、版本、测试和 Owner？

### 动作与治理

- 只读与写工具能否分离？能否按单工具、仓库、环境和任务授权？
- 是否支持短期身份、委托链、批准有效期和完整审计？
- 是否有沙箱、网络/密钥隔离、Safe Output、预算和 Kill Switch？
- 能否阻止 Agent 修改测试/Policy 后自行宣布通过？

### 效果与经济性

- 能否在企业任务上离线评测、在线对照和事故回放？
- 能否按任务报告成功、人工介入、回归、错误类型和单位成功成本？
- Vendor Benchmark 是否公开数据、任务、版本和局限？

### 平台与退出

- 是否支持多模型、自有模型或至少清晰的数据边界？
- Agent/Skill/MCP 是否可版本化、导出、签名和回滚？
- 与代码仓、Pipeline、制品、IAM 和可观测平台的集成是否形成新的锁定？

## 八、主要风险与控制建议

| 风险 | 业务后果 | 优先控制 |
|---|---|---|
| Agent 通过共享身份执行 | 无法追责，权限长期扩散 | 每任务身份、短期 Token、委托链 |
| PR/日志/工具描述提示注入 | 数据泄露或越权动作 | 输入不可信、工具白名单、网关、网络隔离 |
| 为变绿而删除测试/弱化 Gate | 缺陷进入发布 | 规则保护、职责分离、隐藏/未来测试 |
| Agent 修改自己的 Oracle | CI 变绿但问题未愈 | Validator 独立身份、禁止 Skip/Ignore/降阈值、完整 Gate 重跑 |
| Agent 无限构建和重试 | CI 拥塞、成本失控 | 任务预算、最大轮次、熔断和升级 |
| 模型更新后能力漂移 | 原有自治边界失效 | 版本固定、回归评测、自动降权 |
| 多 Agent 冲突或责任模糊 | 重复动作、错误扩散 | 明确任务所有者、Judge/Policy、动作幂等 |
| 过早开放生产 L4 | 大范围变更失败 | 渐进范围、预批准 Runbook、回退与接管 |
| 只追踪使用率和生成量 | 速度上升但稳定性下降 | 联合任务、DORA、安全和经济性指标 |

## 九、2027—2028 展望

### 高概率演进

1. **Agent 成为 Pipeline 一等执行单元。** 主流代码仓、CI 和可观测平台都会提供 Agent Step、Tool Server 和审计。
2. **Context/Tool Plane 成为平台核心。** MCP/Skills 从开发者自助连接走向企业注册、签名、授权和评测；CLI/Skill 接口工厂将扩大可接入的长尾软件范围。
3. **专业 Oracle 不会消失。** 测试、安全、制品、Policy 和 SLO 的确定性事实反而更重要。
4. **CI 计算成为 Agent 生产力瓶颈。** 高频多轮验证推动 Runner、缓存、分片、环境和成本模型重构。
5. **人员转向监督与系统设计。** 平台、QA、安全和 SRE 将共同运营 Agent 自治等级。

### 中概率演进

1. 发布就绪、渐进发布和低风险恢复出现更多受限 L4。
2. 制品仓和供应链平台成为 Agent 依赖选择、签名与溯源的可信入口。
3. 企业形成类似“Agent SRE”的运行职能，持续处理版本、评测、权限、成本和事故。
4. 跨平台上下文图与任务身份标准逐步成形，但产品实现仍碎片化。

### 不建议作为规划基线

1. 关键生产发布在 2028 年前普遍无人监督。
2. 一个通用 Agent 取代代码仓、CI、安全、测试、制品、部署和可观测专业平台。
3. 更强模型自动解决权限、审计、回滚、合规和责任问题。

## 十、最终建议

企业不应把 Agentic CI/CD 定义为“用 AI 自动化更多步骤”，而应定义为：

> 在可验证、可授权、可审计和可恢复的工程系统中，让 Agent 承担高频的上下文理解、计划、工具调用和反馈闭环，并把人类注意力集中到意图、风险、异常和责任。

优先投资顺序应是：

1. 结构化上下文和快速、可靠的 CI 反馈；
2. 任务身份、受控工具、沙箱、审计和评测；
3. 评审、CI 修复、安全修复和只读事故调查的 L2；
4. 非生产与低风险生产动作的 L3；
5. 只有在长期任务证据充分时，才为具体场景开放 L4。

若企业平台质量、测试和权限基础薄弱，Agent 会更快放大混乱；若这些基础健全，Agent 才可能把 CI/CD 从固定自动化系统升级为受治理的自适应交付系统。

## 研究下钻

- [[90_report/seven-dimension-analysis|七维交叉分析工作台]]
- [[00_sources/README|L0 信息源与 Source Brief]]
- [[00_sources/agentic-cicd-source-landscape|81 条核心一手资料景观]]
- [[00_sources/source-pruning-2026-07-14|信息源精简审计]]
- [[05_case_library/README|实践案例库]]
- [[10_summaries/tools/README|Agent 工具与技术栈总结]]
- [[20_summaries/companies/README|公司维度总结]]
- [[30_summaries/stages/README|八阶段总结]]
- [[40_summaries/crosscutting/README|工具、流程、人员与治理变化]]
- [[50_deepdives/README|专题深研索引]]
- [[50_deepdives/cicd-self-healing/README|CI/CD 问题自愈专题]]
