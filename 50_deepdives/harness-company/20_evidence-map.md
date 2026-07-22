---
title: Harness 公司 Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - company/harness
status: complete
as_of: 2026-07-22
---

# Harness 公司 Claim—Evidence—Gap Matrix

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 |
|---|---|---|---|---|
| HAR-C01 | Harness 的当前战略是覆盖代码完成后的软件交付 | [Harness AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/)、[公司介绍](https://www.harness.io/company/about-us) | 是厂商自我定位，不代表每一阶段能力同等成熟 | high for positioning |
| HAR-C02 | AI 能力分布在 UI、IDE 和 Pipeline 三个工作面 | [Core Capabilities](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/) | 各入口的模型、权限和可获得性不同 | high |
| HAR-C03 | DevOps Agent 已能创建/修改多模块 Pipeline、资源、OPA Policy 和 GitOps 对象 | [DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/) | 仅 Harness UI；不支持 BYOM；50 Stage 是厂商自测 | high for mechanism, medium for outcome |
| HAR-C04 | Worker Agent 是 Pipeline 原生可复用 Agent Step | [Worker Agents](https://developer.harness.io/docs/platform/harness-ai/harness-agents/) | 不是把所有普通 Step 自动变成 Agent；账户仍可能需 Support 开通 | high |
| HAR-C05 | Worker Agent 支持 Harness Managed 和自带模型连接器 | [Model Connector](https://developer.harness.io/docs/platform/harness-ai/model-connector/)、[Worker Agents](https://developer.harness.io/docs/platform/harness-ai/harness-agents/) | DevOps Agent/Chat 不支持 BYOM；Provider 范围仍有限 | high |
| HAR-C06 | Marketplace 提供 Managed、Certified 和 Community Agent | [Worker Agents](https://developer.harness.io/docs/platform/harness-ai/harness-agents/) | Certified/Community 数量、审查深度和 SLA 未公开 | high for catalog, low for quality outcome |
| HAR-C07 | Knowledge Graph + HQL 用结构化 Schema 和显式关系约束分析 | [Deterministic by Design](https://www.harness.io/blog/why-harness-ai-uses-knowledge-graph) | Token 节省和准确率为 Harness 架构估算，缺独立复现 | high for design, medium for claimed gain |
| HAR-C08 | Harness 将 Knowledge Graph 用于理解、MCP 用于行动和长尾扩展 | [Knowledge Graph Design](https://www.harness.io/blog/why-harness-ai-uses-knowledge-graph)、[MCP Docs](https://developer.harness.io/docs/platform/harness-aida/harness-mcp-server/) | 外部 MCP 的数据语义、质量和权限仍由客户负责 | high |
| HAR-C09 | MCP 通过少量通用 Verb + Resource Registry 降低 Tool Schema 成本 | [MCP Docs](https://developer.harness.io/docs/platform/harness-aida/harness-mcp-server/)、[开源仓库](https://github.com/harness/mcp-server) | 文档与仓库的资源数/Toolset 数正在快速漂移 | high |
| HAR-C10 | MCP 支持按需 Describe/Schema 的渐进发现 | [Agent Loop Architecture](https://www.harness.io/blog/agent-loop-new-os)、[MCP Docs](https://developer.harness.io/docs/platform/harness-aida/harness-mcp-server/) | MCP Server 本身不实现 Agent Loop、任务状态或最终 Policy | high |
| HAR-C11 | Worker Runtime 在容器和隔离 VM 中运行，可用 Harness Cloud 或客户 K8s | [Worker Agents](https://developer.harness.io/docs/platform/harness-ai/harness-agents/) | CD/Custom 需 Containerized Step Group；SMP 并不等于支持全部 Harness AI | high |
| HAR-C12 | Worker Agent 采用四层隔离：镜像、进程、Secret Broker、Egress Proxy | [Isolation Architecture](https://www.harness.io/blog/how-we-secured-ai-worker-agents-in-harness) | 红队与 CVSS-9.0 回放为第一方测试，尚无独立审计细节 | high for mechanism, medium for effectiveness |
| HAR-C13 | 存在触发 Principal 时，Worker 权限按触发人 RBAC 与声明 Grant 求交集 | [Worker Agents 7/20](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)、[Identity and Permissions](https://www.harness.io/blog/identity-and-permissions-for-ai-worker-agents-in-harness) | [Agent permissions 7/15](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/)又称 Token 独立于 Pipeline Author；事件 Trigger 仍无 scoped token，形成阻断性文档冲突 | medium-high for intended model, low until account validation |
| HAR-C14 | 第三方 MCP 工具权限由 Connector Allowlist 与 Agent Allowlist 取交集 | [Identity and Permissions](https://www.harness.io/blog/identity-and-permissions-for-ai-worker-agents-in-harness) | 只有工程博客披露，产品文档缺少同粒度字段与审计导出说明，需账户/API 回归 | medium-high for design |
| HAR-C15 | AI Rules 是软引导，OPA Policy 才是硬门禁 | [Harness AI Rules](https://developer.harness.io/docs/platform/harness-ai/harness-ai-rules/) | 企业若只配置 Rules，不能保证生成结果满足要求 | high |
| HAR-C16 | Code Review、Coverage、AutoFix 已形成职责链，但不是一个共享 Runtime | [Code Quality Agents](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/)、[Code Repository AI Agents](https://developer.harness.io/docs/code-repository/pull-requests/ai-agents/) | PR Agent/Execute API 使用 Run Step、PAT/LLM Key；Managed Worker 才能按 Worker Runtime 评估 | high for workflow, medium for implementation mapping |
| HAR-C17 | AutoFix 可从日志诊断到修复 PR，Marketplace Worker 版本可重触发 Build | [Code Quality Agents](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/)、[Worker Launch](https://www.harness.io/blog/introducing-autonomous-worker-agents) | 重触发循环不是所有 AutoFix 入口共有，也不等于自动合并或全类型 CI 自愈 | high for differentiated workflows |
| HAR-C18 | AI Test Automation 用自然语言、DOM/视觉、专项 Agent、缓存和 Smart Selector | [AIT Overview](https://developer.harness.io/docs/ai-test-automation/get-started/overview/)、[Intent Testing](https://developer.harness.io/docs/ai-test-automation/get-started/intent-driven/) | 完整模块仍需销售/团队开通；Locator 自愈可能隐藏真实 UI 变化 | high |
| HAR-C19 | AI Test Automation 已有三个署名客户实践 | [Gameopedia](https://www.harness.io/case-studies/gameopedia)、[Wasimil](https://www.harness.io/case-studies/wasimil)、[Siemens Healthineers](https://www.harness.io/case-studies/siemens-healthineers) | 均为供应商发布案例，样本小且没有对照实验 | medium-high |
| HAR-C20 | AI SRE 将沟通记录、Change Correlation、RCA 和 Runbook 串联 | [AI SRE Overview](https://developer.harness.io/3k-docs/ai-sre/get-started/overview/)、[RCA Agent](https://developer.harness.io/3k-docs/ai-sre/ai-agent/rca-change-agent/)、[Runbooks](https://developer.harness.io/docs/ai-sre/runbooks/) | Scribe、Postmortem、Investigator Pipelines 仍有 Support/Flag/EA 条件 | high for architecture, mixed for availability |
| HAR-C21 | Harness AI 总览大多数能力标为 GA | [Harness AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/) | GA 功能组件与模块可获得性不是同一状态；Hosted MCP 等也可能需开通 | high for discrepancy |
| HAR-C22 | AI 输入不用于训练第三方或 Harness 模型 | [2026 Subscription Terms](https://www.harness.io/legal/subscription-terms) | 2023 AI Privacy 页面仍描述 Engagement Data 用于改进；需 DPA/Order Form 确认 | high for contract text, medium for operational interpretation |
| HAR-C23 | Worker Managed LLM 计费将在 2026-08 后单列 | [Worker Agents](https://developer.harness.io/docs/platform/harness-ai/harness-agents/) | 具体费率和配额未公开，PoC 成本不能按当前免费期外推 | high |
| HAR-C24 | 已出现大型企业采用信号 | [Verint/United 发布案例](https://www.harness.io/blog/introducing-autonomous-worker-agents)、[Workday 选择 Harness](https://www.harness.io/press-and-news/harness-selected-by-workday-to-power-agentic-ai-software-delivery-at-enterprise-scale) | Verint/United 是首发引语；Workday 是采用计划，不是量化结果 | medium |
| HAR-C25 | Worker 未声明 `permissions` 时仍会获得文档化默认只读权限 | [Worker Agents 7/20](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) | 声明 block 后默认项不合并；Managed Connector 还需 `ai_llm_gateway: access`；与“No ambient permissions”措辞存在张力 | high for current docs, account validation required |
| HAR-C26 | Scoped Token 对所在 Stage 或 Containerized Step Group 的每个 Step 生效 | [Agent permissions 7/15](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/permissions/) | Blast Radius 不只覆盖 Agent 内核；旁路脚本/插件也可能得到 Token，应拆最小 Stage/Step Group | high for current docs |
| HAR-C27 | AgentTrace 与开源 `harness-evals` 可把 Agent Trace、Threshold 和 Exit Code 接入 CI；Harness AI Evals SaaS 仍为 Beta | [AgentTrace](https://www.harness.io/blog/introducing-agent-trace)、[AI Evals](https://www.harness.io/blog/introducing-ai-evals)、[harness-evals](https://github.com/harness/harness-evals) | 概率 Eval/LLM Judge 不能替代 Test、Scanner、OPA、Signature、SLO 和人工 Review | high for open-source mechanism, beta for SaaS product |
| HAR-C28 | Zero Trust Service 可在 Task 执行前调用客户 Validator 并 fail closed | [ZTS engineering article](https://www.harness.io/blog/building-a-zero-trust-service-for-ci-cd-how-we-intercept-every-task-before-it-executes) | 工程披露并要求联系 Account Team；不是 Worker 的默认 GA 组成 | medium-high for reference architecture |

## 关键证据缺口

- 缺少相同企业任务集上 Harness、GitHub、GitLab、AWS 与独立 Agent 的第三方对照评测；
- 缺少 Worker Agents 跨语言、跨失败类型、长期回归、误操作和每成功任务总成本；
- 缺少 Marketplace Certified/Managed Agent 的公开版本、签名、SBOM、评测门槛和回滚策略；
- 缺少 Worker Runtime 四层隔离与委托身份机制的独立红队或审计报告；
- 缺少 Knowledge Graph 数据延迟、实体覆盖、跨工具冲突和 HQL 查询正确率的外部证据；
- 缺少生产部署、制品晋级和事件恢复中普遍无人值守 L4 的可信客户证据。
- 缺少 7/15 与 7/20 两份权限文档冲突的版本化解释，以及可导出的 Effective Permission/Principal 验证证据；
- 缺少 Code Quality PR/API 实现与 Managed Worker 实现之间的统一版本、镜像签名、SBOM、凭据路径和迁移说明。
