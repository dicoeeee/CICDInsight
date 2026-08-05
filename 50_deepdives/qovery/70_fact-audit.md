---
title: Qovery LLM/CI/CD 逐主张事实审计
tags:
  - research/agentic-cicd
  - research/fact-audit
  - company/qovery
status: passed-with-gaps
as_of: 2026-08-03
confidence: high-for-existence-medium-for-autonomy-and-outcomes
presentation_ready: true
---

# Qovery LLM/CI/CD 逐主张事实审计

## 审计结果

- **核心 Claim：**22 项；均有直接一手证据、明确的厂商自述标签或分析标识；
- **研究 Subagent：**`gpt-5.6-terra` / `high` 通过网络检索 Qovery Changelog、当前文档、官方博客和 GitHub，底稿为 [[00_sources/research-qovery-llm-cicd-capabilities-2026-08-03|Qovery LLM/CI/CD 研究]]；
- **主 Agent 复核：**重新打开 Alpha/Closed Beta/Self-service Beta、MCP/Skills/Deployment Troubleshooting、Role/Confirmation/RDE/Cluster-state Changelog，以及 Copilot、MCP、Capability pages 和 `Qovery/qovery-skills`；
- **状态口径：**Copilot Beta；MCP/Skills 已发布、状态未标；RDE Early Access；Agentic Workflow closed access / coming next；
- **权限口径：**Tool visibility、Read-only/Read-write、RBAC/token、确认、外部 Gate 分层；
- **效果口径：**没有独立 LLM 客户 ROI、准确率、成功率或 TCO 数据，不回填传统 Qovery 案例指标；
- **负面边界：**2024 路线图能力没有后续正式发布证据时，只保留为历史预告；未检索到不等于不存在。

## 主张门禁核对

| 正式结论 | 直接证据 | 关键限制 | 结果 |
|---|---|---|---|
| Copilot 从 2025 Alpha 演进为当前 Beta | 2025-05、2025-11、2026-01 Changelog，当前 Overview | Beta 不是 GA；子能力状态不统一 | passed |
| MCP 和 Skills 是两个不同 Agent 入口 | MCP docs、AI Agent Quickstart、Skills repo | MCP 管理既有 Infra；Skills 从代码部署；均未标 GA | passed |
| Copilot 可诊断部署失败并给出修复建议 | 2026-03 Changelog、Troubleshooting docs | Root cause/fix 部分支持；无准确率和独立效果 | passed-with-gap |
| Skills 可把代码分析延伸到 Environment 部署 | Quickstart、Deploy Skill、Skills README | 生成配置正确性和生产适配未验证 | passed-with-gap |
| MCP 默认只读，写入需显式多重开关与 RBAC | MCP docs/interface | Console/Slack 写状态文档冲突；读操作也可能敏感 | passed |
| Dedicated Role、写确认、Audit 和 Secret path 构成治理面 | 2026-06/07 Changelog、Getting Started | 不等于业务 Release authority | passed |
| Cluster state 为 Agent 增加结构化 Kubernetes 诊断输入 | 2026-07-15 Changelog | 不证明根因或修复正确 | passed |
| KRR recommendation 可被 Agent 使用，但 KRR 本身不是 LLM | 2026-07-15 Changelog | 依赖 Observability；Action 仍需授权 | passed |
| Preview/Rollback 是既有原语，AI 新增的是编排 | Preview docs、Deploy page、Preview Skill | 不把旧功能重新归类为 AI 创新 | passed-as-analysis |
| Skills 是可执行治理资产，不是无副作用提示文本 | Official Skills repo/SKILL.md | 含 telemetry、API 命令、自动修复与重试 | passed |
| Skill 初始确认与局部自动修复存在不同授权语义 | Deploy Skill | 不能泛化为托管 Copilot；需企业重写 | passed-as-conflict |
| Ticket → Sandbox → Test → PR 仍为受限预览方向 | 2026-06 Blog、2026-07 Changelog | closed access / coming next，不是 GA | passed |
| Qovery LLM 层补充而非替代 CI/GitOps | How Qovery Works、Argo CD integration、Deploy page | 最终所有权取决于企业集成和 Gate | passed-as-analysis |
| 现有证据足以支持机制页 | 22 个 Claim 与状态/权限审计 | 不支持效果性和全自治主张 | passed |

## 证据冲突与处理

### 1. Console Read-write

当前 Getting Started 页面前半部分称 Console Read-write 可由管理员开启，后半部分又称 Console 永久只读；Console 专页也称只读。MCP 当前文档与产品页对读写双开关最清楚。

**处理：**正式材料只确定 MCP 可受控读写；Console 写能力标 `conflicting`，Slack 保守写只读。

### 2. Sonnet 4.5 与 Sonnet 5

Copilot Overview 写 Claude Sonnet 4.5，2026-07-15 Changelog 写已迁移 Sonnet 5。

**处理：**不把当前 Model 版本作为正式主张；如需回述，写“Qovery 最新 Changelog 自述 Sonnet 5，当前 Overview 尚未同步”。

### 3. Capability Matrix 的 2025-01-15 验证日期

多项 Capability 的 `Last Validated` 早于 2025-05 Alpha 公告。

**处理：**矩阵只证明当前文档声称的 Support level，不用作历史发布日期、GA 或独立测试证据。

### 4. Log access 范围

Deployment Troubleshooting 说分析 Application log；Console Privacy 说 Console Copilot 不访问 Application log content。

**处理：**分开 Interface；只写产品面存在 Log analysis，不写每个入口都能读全部日志。

### 5. 托管 Copilot 与开源 Skill 的写入确认

2026-06 Changelog 说 Copilot 每个写动作确认；Deploy Skill 允许对 Skill 创建的 Dockerfile 和 Qovery config 自动修复并最多重试三次。

**处理：**托管服务合同与本地 Skill 规则分开；生产采用必须由外部 Policy 覆盖 Skill 默认行为。

### 6. Skill telemetry 与 Audit

SKILL.md 要求立即 POST `/skill-tracking`；2026-07 Changelog 说 Skill invocation 进入 Audit。公开材料未解释两者 Payload、Retention、Opt-out 和对应关系。

**处理：**把 telemetry 标为独立外部写和采购/数据治理核验项，不称 Skills 为纯本地资产。

### 7. Agentic Workflow 的完成式营销表述

产品页和博客用完成式描述 Ticket → Sandbox → PR；最新 Changelog 仍写 closed access / coming next。

**处理：**以带日期 Changelog 的产品状态为准，正式报告标为受限预览方向。

## 负面搜索边界

本轮在 Qovery 官方 Changelog、文档、博客、产品页、客户案例和 GitHub 范围内，未识别到可以独立复核的 Copilot Root cause accuracy、Fix success、MTTR、Lead time、Change failure rate、人工节省、Token cost、误变更率或完整 TCO。该结果只证明公开一手材料的证据缺口，不证明客户没有收益。

Syment 等现有数字来自传统 Qovery 使用，且时间早于 MCP/Skills 发布；不作为 LLM 效果证据。

## Presentation-ready 决定

**结论：通过，但范围受限。**可使用以下单一主张：

> **Qovery 没有让 LLM 取代 CI/CD，而是把 Environment graph、部署引擎与运行证据变成 Agent 可读、可计划、可受控执行的接口；生成式决策进入交付链，最终授权仍由 RBAC、确认和外部确定性 Gate 持有。**

页面必须保留：

1. Copilot 为 Beta；
2. MCP/Skills 已发布但未标 GA；
3. Agentic Workflow 为 closed access / coming next；
4. Console 写能力文档冲突；
5. Skill 自动修复、Telemetry 和托管 Copilot 语义分开；
6. 没有独立客户效果数据；
7. Preview、Rollback 和 Deployment Engine 是既有平台原语。

## 不阻塞该页面的剩余缺口

模型版本、Console 写模式、Skill telemetry 合同、独立客户效果、区域/套餐矩阵、诊断评测集、生成配置安全性和大规模 TCO 会影响采购与生产标准化，但不推翻“Agent 入口复用受治理交付控制面”的机制主张。
