---
title: Qovery LLM/CI/CD 专题研究边界
tags:
  - research/agentic-cicd
  - research/deep-dive
  - company/qovery
status: complete
as_of: 2026-08-03
---

# Qovery LLM/CI/CD 专题研究边界

## 决策目标

回答 CTO、研发效能负责人和平台工程负责人五个问题：

1. Qovery 在 CI/CD、部署、Preview、验证与恢复链中实际新增了哪些 LLM 能力；
2. Copilot、MCP Server、Qovery Skills、RDE / Agentic Workflow 分别位于哪一层，是否已正式可用；
3. LLM 读取什么上下文、产生什么输出、能否执行动作、谁持有最终授权；
4. 这些能力相对传统 Qovery CD 原语究竟增加了什么，而不是换一套营销名词；
5. 哪些机制足以进入企业试点，哪些仍需要独立验证或保持阻塞。

## 核心主问题

> Qovery 是否把 LLM 从“回答 DevOps 问题”推进为“能够基于实时环境状态、调用受控交付原语并对结果继续观察的 Agent 接口”？如果是，闭环在哪些节点已经成立，在哪些节点仍只是候选动作、产品示例或 Roadmap？

## 纳入范围

- Qovery DevOps / AI Copilot 从 Alpha、Closed Beta 到 Self-service Beta 的演进；
- Console、Slack、MCP 三种入口的读写模式和权限差异；
- 部署、重部署、环境创建/克隆、配置更新、扩缩容、取消与回滚等自然语言动作；
- 部署失败、Build failure、Runtime error、Health check、网络、资源和 Kubernetes state 的诊断；
- Scheduled / recurring task 与后台长任务；
- Qovery 开源 Skills：`qovery`、`onboard`、`deploy`、`troubleshoot`、`optimize`、`speedup`、`preview`、`terraform`；
- Codebase analysis、Dockerfile/配置/Terraform 生成、部署监控、候选修复和验证；
- MCP OAuth/API token、默认只读、显式读写、RBAC、审计和 Cluster state；
- 专用 Copilot Role、写动作确认、Secret path 绑定、短时只读 Kubernetes 凭证；
- RDE / Agent Sandbox 与 Linear/Jira Ticket → Sandbox → Code → Deploy → Test → PR 流程；
- 支撑解释这些能力所必需的传统 Qovery 部署、Environment、Pipeline、Preview 和 BYOC/Kubernetes 基线。

## 时间窗口

- **主观察窗口：**2024-01-01 至 2026-08-03；
- **首次可核验发布节点：**2025-05-20 Copilot Alpha；
- **当前状态基线：**2026-07-15 最新 Changelog 与截至 2026-08-03 的当前文档/官方 GitHub；
- **刷新周期：**30 天。该产品在 2026 年以两周级节奏迭代，状态和权限口径可能快速变化。

## 非目标

- 不做 Qovery 全产品、全部 Cloud/Kubernetes 功能或采购价格横评；
- 不把 Qovery 已有 Preview Environment、Rollback、日志、指标、Deployment Pipeline 本身写成新 LLM 能力；
- 不评价底层模型通用编码能力，不做 Claude/Codex/Cursor 模型 Benchmark；
- 不把产品示例中的 Blue/Green、Canary、Multi-region failover、DB migration 自动化直接视为已验证能力；
- 不把 MCP/CLI/API 可调用等同于获准执行；
- 不在缺少真实组织、Token、Cluster 和写授权时运行部署、变更或恢复实验；
- 不将厂商时间节省、速度、零泄漏或普遍 ROI 表述为独立事实。

## 核心术语

- **Copilot：**Qovery 托管的 LLM/Agent 体验，读取平台上下文并计划或执行 Qovery 作业；当前品牌状态为 Beta。
- **MCP Server：**向外部 MCP Client 暴露 Qovery 实时状态与工具调用面的远程接口；Day-2 管理为主。
- **Qovery Skills：**安装到 Coding Agent 的开源 `SKILL.md` 作业包，通过 CLI/API/MCP/Terraform 使用 Qovery。
- **Control Plane：**Qovery 接收意图、维护 Environment/Service/Pipeline 状态、触发 Engine 并提供 RBAC/Audit/API 的平台面。
- **Environment Graph：**应用、Database、Helm、Terraform、Job、变量、依赖和阶段组成的部署单位；这是本专题的分析抽象，不声称是 Qovery 官方内部对象名。
- **Candidate Fix：**LLM 或 Skill 生成的修复建议/补丁，尚未由独立 Gate 证明正确。
- **Execution Authorization：**RBAC/token、组织读写开关、逐动作确认、环境保护与外部测试共同形成的执行许可。
- **Agentic Workflow：**Ticket 触发隔离环境，编码 Agent 实现、部署、测试并返回 PR/Preview 的目标流程；截至观察日为 closed access / coming next。

## 证据口径

| 等级 | 来源 | 可支持 | 不可直接支持 |
|---|---|---|---|
| A | Qovery 带日期 Changelog、当前官方文档、官方 GitHub 源码/Skill | 功能存在、接口、文档状态、声明的权限流程和 Skill 规则 | 独立效果、正确率、生产成熟度 |
| B | Qovery 官方技术博客、产品页、Demo | 架构意图、场景、厂商自述流程 | GA、普遍 ROI、独立客户结果 |
| C | 客户自己的工程材料 | 该客户在文中时点的架构与结果 | 当前持续使用、行业平均、单因素因果 |
| D | 第三方目录或二次介绍 | 检索线索 | 正式结论 |

## 验收标准

- 必需交付物全部完成，并形成逐主张 Evidence Map；
- 至少覆盖 Copilot、MCP、Skills、RDE/Agent Workflow 四层，不以单一功能代替公司判断；
- 所有“新功能”与传统 Qovery 原语分开；
- 所有能力标注状态、入口、上下文、输出、执行边界和证据缺口；
- 对 Console 读写、底层模型版本、能力矩阵日期和 Skill 自动修复等冲突保留原貌；
- 明确区分诊断、建议、候选修复、执行、验证和最终 Gate；
- `presentation_ready` 前完成事实审计，禁止效果性表述越过证据。
