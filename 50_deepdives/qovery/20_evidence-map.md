---
title: Qovery LLM/CI/CD Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - company/qovery
status: complete
as_of: 2026-08-03
confidence: high-for-existence-medium-for-autonomy-and-outcomes
---

# Qovery LLM/CI/CD Claim—Evidence—Gap Matrix

## 证据标记

- **A：**带日期的 Qovery Changelog、当前官方文档、官方 GitHub 源码/Skill；可证明接口、功能存在、状态标签和书面规则。
- **B：**Qovery 官方博客、产品页和 Demo；可证明厂商设计意图和自述场景，不能证明独立效果。
- **分析：**主 Agent 根据多个一手事实形成的分层、控制边界或采用判断。
- **访问时间：**下列网页均于 `2026-08-03` 复核。

## 核心 Claim

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 | 审计状态 |
|---|---|---|---|---|---|
| QV-C01 | Qovery Copilot 于 2025-05 以 Alpha 公开，初始能力包括排程、报告、环境控制、配置解释和优化建议 | [2025-05-20 Changelog](https://www.qovery.com/changelog/2025-05-21)（A） | Alpha、需早期访问；不证明生产成熟度 | high | passed |
| QV-C02 | Copilot 于 2025-11 进入 Closed Beta，2026-01 开放组织自助启用 | [2025-11-04 Closed Beta](https://www.qovery.com/changelog/2025-11-05)；[2026-01-27 Self-service Beta](https://www.qovery.com/changelog/2026-01-28)（A） | 2026-01 当时仅只读；品牌截至观察日仍为 Beta | high | passed |
| QV-C03 | Copilot 的核心不是问答，而是将自然语言计划映射为多步 Qovery 基础设施作业 | [Copilot Overview](https://www.qovery.com/docs/copilot/overview)；[Technical blog](https://www.qovery.com/blog/how-we-built-an-agentic-devops-copilot-to-automate-infrastructure-tasks-and-beyond)（A/B） | “Agentic”“self-correct”是厂商定义；没有任务成功率/轨迹评测 | medium-high | passed-with-gap |
| QV-C04 | Copilot 可将一次性/周期性部署、环境启停和清理意图变成后台任务 | [Copilot Overview](https://www.qovery.com/docs/copilot/overview)；[2026-01-27 Changelog](https://www.qovery.com/changelog/2026-01-28)（A） | 示例中的条件判断、通知和验证不等于每种组合均已实测 | medium-high | passed-with-gap |
| QV-C05 | 2026-03 原生 Deployment Troubleshooting 会关联部署日志、应用日志和部署历史并给出根因/修复建议 | [2026-03-24 Changelog](https://www.qovery.com/changelog/2026-03-25)；[Troubleshooting docs](https://www.qovery.com/docs/copilot/capabilities/troubleshooting)；[2026-03-30 Blog](https://www.qovery.com/blog/stop-guessing-start-shipping---ai-powered-deployment-troubleshooting)（A/B） | 当前可证明诊断和建议；“自动修复/根因正确”没有独立验证 | high-for-existence-medium-for-quality | passed-with-gap |
| QV-C06 | MCP Server 于 2026-02 live，可让外部 Agent 读取并操作既有 Qovery 基础设施 | [2026-02-10 Changelog](https://www.qovery.com/changelog/2026-02-11)；[MCP docs](https://www.qovery.com/docs/copilot/mcp-server)（A） | MCP 是接口，不是独立授权；首次从代码部署应使用 Skills | high | passed |
| QV-C07 | MCP 默认只读，写入需显式 `read_write=true`、组织启用与 Qovery RBAC/token 权限 | [MCP interface](https://www.qovery.com/interfaces/mcp-server)；[MCP docs](https://www.qovery.com/docs/copilot/mcp-server)（A） | OAuth/Token 泄漏、过宽 Role 与敏感读结果仍需客户治理 | high | passed |
| QV-C08 | 2026-07 MCP 新增 Kubernetes object state 查询，将 Pods、Networking、Certificates、Nodes 等结构化状态提供给 Agent | [2026-07-15 Changelog](https://www.qovery.com/changelog/2026-07-15)（A） | 查询状态不等于根因正确，也不产生修复授权 | high | passed |
| QV-C09 | 2026-04 发布的 Qovery Skills 将 Coding Agent 连接到 CLI/API/MCP/Terraform，形成从代码到部署的作业流 | [2026-04-21 Changelog](https://www.qovery.com/changelog/2026-04-22)；[Official repository](https://github.com/Qovery/qovery-skills)（A） | Skill 是可变的提示/作业资产，不是 Qovery SaaS GA 或模型质量保证 | high | passed |
| QV-C10 | 当前官方仓库提供 8 个 Skills：router、onboard、deploy、troubleshoot、optimize、speedup、preview、terraform | [qovery-skills README](https://github.com/Qovery/qovery-skills)（A） | 数量会变化；必须以版本/Commit 固定试点输入 | high | passed |
| QV-C11 | `qovery-deploy` 能分析代码库、生成缺失的 Dockerfile、配置依赖/变量/阶段并部署后观察 | [Deploy Skill](https://github.com/Qovery/qovery-skills/blob/main/qovery-deploy/SKILL.md)；[AI Agent Quickstart](https://www.qovery.com/docs/getting-started/quickstart/ai-agent)（A） | 生成正确性、Framework 覆盖和生产适配未独立验证；执行前仍需账户/Token/Cluster/Git 连接 | high-for-workflow-medium-for-quality | passed-with-gap |
| QV-C12 | `qovery-troubleshoot`、`speedup` 和 `optimize` 将日志/历史/指标/KRR 等事实编排为诊断、优化和报告 | [qovery-skills README](https://github.com/Qovery/qovery-skills)；[2026-07-15 KRR Changelog](https://www.qovery.com/changelog/2026-07-15)（A） | KRR/时序测量是确定性输入；LLM 的排序、解释和修复仍需验证；KRR 依赖 Observability | medium-high | passed-with-gap |
| QV-C13 | `qovery-preview` 让 Agent 发现/克隆 Blueprint、切换 PR branch、部署、验证和清理 Preview | [Preview Skill](https://github.com/Qovery/qovery-skills/blob/main/qovery-preview/SKILL.md)；[Preview docs](https://www.qovery.com/docs/configuration/environment)（A） | Preview Environment 是既有 Qovery 能力；新的是 Agent 编排，不是底层环境原语 | high | passed |
| QV-C14 | Skills 的部署计划含显式确认门禁，但后续自动修复可不再次请求权限 | [Deploy Skill](https://github.com/Qovery/qovery-skills/blob/main/qovery-deploy/SKILL.md)（A） | 与托管 Copilot“每个写动作确认”的产品口径不同；需要企业重写/Policy 封装 | high | passed-as-conflict |
| QV-C15 | Skills 在开始执行前要求向 Qovery `/skill-tracking` 发送使用事件 | [Deploy Skill](https://github.com/Qovery/qovery-skills/blob/main/qovery-deploy/SKILL.md)；[Preview Skill](https://github.com/Qovery/qovery-skills/blob/main/qovery-preview/SKILL.md)（A） | 是外部写/遥测；失败被忽略且输出丢弃，需在供应链和数据治理中显式审计 | high | passed |
| QV-C16 | Dedicated Copilot Role 可将 Agent 权限与当前用户权限分开 | [2026-06-03 Changelog](https://www.qovery.com/changelog/2026-06-03)；[Getting Started](https://www.qovery.com/docs/copilot/getting-started)（A） | Role 过宽会让多用户共享更大权限；不是天然最小权限 | high | passed |
| QV-C17 | 2026-06 起 Read-write Copilot 的每次写动作要求显式确认 | [2026-06-17 Changelog](https://www.qovery.com/changelog/2026-06-17)（A） | 当前 Console 文档对是否可写自相矛盾；该结论保守用于已启用的读写通道 | high-for-policy-medium-for-interface | passed-with-conflict |
| QV-C18 | Secret Manager 允许 Agent 只传 Secret path，值在 Deploy 时解析而不进入 Agent Context | [2026-06-17 Changelog](https://www.qovery.com/changelog/2026-06-17)（A） | 只覆盖正确配置的 Secret path 工作流；Logs、Env metadata 和其他工具仍可能暴露敏感信息 | high | passed |
| QV-C19 | RDE / Agent Sandbox 为 Agent 提供隔离环境、Scoped secret、Network control、Auto-shutdown 和 Audit 的方向 | [2026-05-20 RDE Alpha](https://www.qovery.com/changelog/2026-05-20)；[2026-07-01 RDE update](https://www.qovery.com/changelog/2026-07-01)；[Agent Runtime](https://www.qovery.com/solutions/agent-runtime)（A/B） | RDE 为 Early Access；部分治理能力来自产品页，需目标套餐/账户实测 | medium-high | passed-with-gap |
| QV-C20 | Linear/Jira Ticket → Sandbox → Coding Agent → Deploy/Test → PR/Preview 是 Qovery 的目标闭环 | [2026-06-09 Blog](https://www.qovery.com/blog/coding-agents-broken-loop)；[2026-07-15 Changelog](https://www.qovery.com/changelog/2026-07-15)（A/B） | 最新 Changelog 明确 closed access / coming next；不可写成 GA 或普遍可用 | high | passed-with-status-limit |
| QV-C21 | Qovery 当前没有公开一手材料证明 LLM 诊断准确率、修复成功率或跨客户 ROI | 对 Changelog、Docs、Blog、官方 GitHub 的负面检索；[[00_sources/research-qovery-llm-cicd-capabilities-2026-08-03|研究底稿]] | 仅表示截至观察日公开资料未识别到，不证明客户没有收益 | medium-high | passed-as-gap |
| QV-C22 | Qovery 的新意是把 LLM 意图接到既有 Environment/Deployment control plane，而不是替代 Test/Gate/CI Orchestrator | QV-C05—C20 综合（分析） | 需要在每个组织中验证 API 覆盖、GitOps/IaC 回流和 Gate 所有权 | high | passed-as-analysis |

## 证据冲突与缺口

### 1. Console 是否可写

[Getting Started](https://www.qovery.com/docs/copilot/getting-started) 前半部分写 Console Read-write 可由管理员启用，后半部分又称 Console 永久只读；[Console page](https://www.qovery.com/docs/copilot/console) 也称 Console 只读。正式结论只确认 MCP 读写路径与组织/RBAC/确认门禁，不把 Console 写能力写成确定事实。

### 2. Model 版本

[Copilot Overview](https://www.qovery.com/docs/copilot/overview) 仍写 Claude Sonnet 4.5；[2026-07-15 Changelog](https://www.qovery.com/changelog/2026-07-15) 称已升级 Sonnet 5。按带日期的最新公告记录 Sonnet 5，同时保留文档滞后，不将 Model 版本作为专题主张。

### 3. Capability Matrix 日期

Deployment/Troubleshooting/Optimization 页面把多项能力标为 `Last Validated 2025-01-15`，早于 2025-05 Alpha 公告。该字段不能作为历史 GA 或独立测试证据，只能视为当前文档中的能力声明；Dockerfile/Build/Network Optimization 还明确标 `Coming Soon`。

### 4. Copilot 与 Skills 的授权语义

托管 Copilot 的 2026-06 公告要求每个写动作确认；开源 Deploy Skill 则允许在已确认部署后自动修改 Qovery Config 和 Skill 自建 Dockerfile并重试。两者是不同执行主体和合同，不能互相泛化。企业必须在安装 Skill 前重写或外置 Policy。

### 5. 效果与客户证据

当前材料可证明能力入口、工作流和控制点，不足以证明 Root cause 正确率、Fix success、MTTR、Change failure rate、Lead time、Token cost 或人工节省。任何效果性结论继续阻塞。
