---
title: Harness 公司专题分析发现
tags:
  - research/agentic-cicd
  - research/findings
  - company/harness
status: complete
as_of: 2026-07-16
---

# Harness 公司专题分析发现

## F1. Harness 正从“CI/CD 产品”转向“软件交付 Agent 控制面”

Harness 的 2026 产品组合不再只围绕 Pipeline Runner。DevOps Agent 负责设计和修改 Harness 对象；Worker Agents 进入执行面；Knowledge Graph 提供上下文；MCP/CLI/Skills 把平台能力开放给外部 Agent；AI Rules、OPA、RBAC、Approval 和 Audit 负责治理；测试、安全、SRE、FME、FinOps 等模块提供专项 Agent 和确定性事实。

因此，其竞争单位已经从一条 Pipeline 扩大为一套“意图—上下文—执行—验证—治理”平台。企业采用 Harness AI 的收益上限，与其采用多少 Harness 模块和能否把异构工具数据映射进平台直接相关。

## F2. 三类 Agent 不可混为一个产品

| 类型 | 主要入口 | 核心职责 | 模型治理 | 风险边界 |
|---|---|---|---|---|
| DevOps Agent | Harness UI | 创建/修改 Pipeline、资源、Policy、GitOps，排障 | Harness Managed，不支持 BYOM | 用户审核、RBAC、OPA、模块许可证 |
| Worker Agent | Pipeline Agent Step | 在交付流程中多轮行动 | Managed Connector 或自带 Anthropic/OpenAI | 隔离 Runtime、Scoped Token、MCP Tool Scope、Gate |
| 专项 Agent | Code/AIT/SRE/FME/安全等模块 | Review、Coverage、Test、RCA、Release 等领域任务 | 模块各异 | 领域 Oracle、PR/Runbook/模块权限 |

DevOps Agent 更像“平台配置与交互入口”；Worker Agent 是“可复用执行单元”；专项 Agent 是“产品化任务实现”。它们可以组合，但可用性、数据、模型和自治等级不同。

## F3. 核心技术不是“更强 Prompt”，而是五层系统

1. **语义层：** Knowledge Graph、Schema、显式关系、HQL 和权限过滤；
2. **接口层：** MCP 的少量通用 Verb、Resource Registry、Describe/Schema 渐进发现，另有 Unified CLI/Skills；
3. **编排层：** Pipeline 的顺序、并行、条件、矩阵、循环、Approval、Failure Strategy 和 Rollback；
4. **执行层：** Agent Container、隔离 VM、Harness Cloud 或客户 K8s/Delegate；
5. **治理层：** Rules、OPA、RBAC、Scoped Token、MCP Gateway、Audit、成本和外部 Oracle。

模型被设计成可替换或至少可升级的推理组件，而平台价值集中在交付上下文、动作入口和治理继承。

## F4. Harness 对 MCP 的态度是“行动接口”，不是“上下文数据库”

Harness 一方面重度投资 MCP，开放 11 个左右的通用工具和持续增长的 Resource Registry；另一方面明确反对用原始 API/MCP 作为所有分析的默认路径。其架构选择是：能建模的 Read/Query/Analyze 优先走 Knowledge Graph/HQL；日志等大对象走 Schema-guided Bridge；外部长尾系统才走 MCP；Create/Update/Execute 则由 MCP 承担动作面。

这与 MCP 渐进式加载的行业趋势一致：Tool 名单保持小，Domain Schema 按需加载，复杂度放在 Registry/Dispatcher 后面。它同时说明“拥有很多 MCP Server”不是平台壁垒，结构化上下文和授权才是。

## F5. Pipeline 没有被 Agent 取代，而是成为 Agent 的生产控制结构

Worker Agent 仍是 Step。它前后可以放确定性日志采集、测试、扫描、Policy、Approval、部署和验证；Pipeline 决定触发、预算、顺序、并行和失败策略。成熟复杂场景应使用：

> 确定性预处理 → Agent 推理/修改 → 确定性 Oracle → Policy/Approval → 受控执行 → SLO/回退

“每个 Pipeline Step 都可成为 Agent”应理解为平台允许在各阶段插入 Agent 能力，不应理解为固定脚本、测试、签名和审批都变成概率推理。

## F6. Worker Agent 的安全设计已接近生产架构，但证据仍是第一方

2026-07 两篇技术文章补齐了架构细节：

- 把 Agent 进程视为已被攻陷；
- 移除编译器、包管理器、Setuid 和多余 Capability；
- Agent、Credential Broker、Egress Proxy 使用不同非特权用户；
- 真实 Secret 不进入 Agent 环境，Placeholder 只对目标 Host 有效；
- 网络默认拒绝并强制通过 Allowlist Proxy；
- Agent 以触发人的委托身份运行，Token 只包含声明的权限子集；
- MCP Gateway 允许 `connector.allowedTools ∩ agent.allowedTools`；
- 每次 Tool Call 记录 Agent、Run、Principal、参数和结果。

这是比“模型会拒绝危险 Prompt”更可靠的思路。但它仍需客户验证 Feature Flag、实际 Runtime 配置、所有网络路径、审计字段和 Marketplace Agent 供应链；厂商自报的漏洞回放不是独立安全认证。

还有一个不能被“治理继承”口号掩盖的缺口：当前文档称 Pipeline Trigger 发起的 Worker Run 不会注入 scoped token，因此 Webhook、Schedule、Artifact、Manifest 等事件触发不能直接继承某个用户的权限子集。手动/API 与事件触发必须分开建模；后者在身份方案和审批验证完成前不应获得生产写权限。

## F7. “治理继承”有价值，但不是自动完整

Harness 的优势是模块天然继承平台用户、RBAC、Secrets、Connectors、Audit、Notification 和 Policy。但真正的治理仍需显式设计：

- AI Rules 只是生成前的自然语言指导，不是硬门禁；
- OPA 才能在 Save/Run 阶段拒绝不合规配置；
- Agent Grant 需要声明，部分细粒度权限仍依赖 Feature Flag；
- 第三方 MCP 的工具范围与 Harness 资源 RBAC 是两套边界；
- PR、测试、扫描、签名、SLO 和业务判断仍是外部 Oracle；
- 继承触发人的身份也意味着错误操作最终仍需人和组织承担责任。

## F8. 当前最成熟的公开应用证据集中在测试，不在通用 Worker Agent

Worker Agents 于 2026-06-30 GA，公开案例主要是首发客户引语和推广计划。相比之下，AI Test Automation 已有 Gameopedia、Wasimil、Siemens Healthineers 三个署名案例，且给出了维护时间、失败率、创建时间和发布频率等指标。

但这些仍是厂商发布案例，不能与第三方对照试验等价。通用 Worker Agent 的跨客户 Verified Fix Rate、误操作、长期回归和经济性仍是显著证据缺口。

## F9. 产品“GA”必须拆成三种状态

1. **Capability Status：** 总览是否标记 GA/Beta；
2. **Account Availability：** 是否需销售、Support、Feature Flag 或 Cluster 渐进发布；
3. **Production Readiness：** 客户是否已完成权限、红队、评测、SLA 和成本验证。

AI Test Copilot 在总览为 GA，但完整 AI Test Automation 仍需联系销售/团队启用；AI Scribe 总览为 GA，但详细文档要求联系 Support；Worker Agent 总览 GA，但权限注入和部分账户入口仍可能需开通；Hosted MCP 也可能按账户配置。任何采购清单都应记录三种状态。

## F10. 开放性是不对称的

- Worker Agent 支持自带 Anthropic/OpenAI 连接器，也可用 Harness Managed Connector；
- DevOps Agent/Chat 使用 Harness Managed Provider，不支持 BYOM；
- MCP Server 和 Skills 有官方开源仓库；
- Pipeline、Knowledge Graph、LLM/MCP Gateway、Marketplace 治理仍是 Harness 平台专有能力；
- Self-hosted Kubernetes Worker Runtime 不等于 Harness AI 支持 Self-Managed Platform，官方仍称 AI Chat/DevOps Agent 不支持 SMP。

因此 Harness 不是纯封闭，也不是模型/平台完全可替换。企业要按 Agent 类型分别评估锁定，而不是给整个公司一个“开放/封闭”标签。

## F11. 文档快速变化本身是运营风险

观察日的官方材料同时出现：MCP 10/11 Tools、139/140+/196 Resource Types、Claude Opus/Sonnet 4.6 与 FAQ 中更旧模型、AI 身份/委托身份不同表述、GA 与 Controlled Availability 并存。部分是发布日期快照差异，部分是文档未同步。

企业应固定 MCP Release、Agent Template、模型、Connector、Docs Snapshot 和测试集；升级时重新验证 Tool Schema、权限、成本和结果，不能只看 SaaS 页面显示“已更新”。

## F12. Harness 的真实竞争优势和短板都很清楚

**优势：** Pipeline 是现成生产执行器；跨模块交付图谱；统一 RBAC/OPA/Approval/Audit；Agent 可同时进入 UI、IDE 和 Pipeline；专项测试/SRE/安全/成本模块提供 Oracle。

**短板：** 最大价值依赖平台采用深度；DevOps Agent 与 Chat 的模型选择有限；许多最新能力开通状态复杂；产品更新快导致文档漂移；通用 Agent 的独立效果数据不足；SMP/完全私有化需求受限。

因此 Harness 最适合已经把 Pipeline/模块治理作为交付控制面的企业，或计划将异构工具集中到一个交付语义层的企业；只想在现有 CI 上添加一个轻量 Coding Agent 的团队，可能承担过高的平台迁移成本。
